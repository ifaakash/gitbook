---
icon: aws
---

# How to design aECS Service

## Example Codebase

```terraform
resource "aws_ecs_service" "modx_app" {
  name                              = "${var.environment}-${var.project_name}-service"
  cluster                           = aws_ecs_cluster.main.id
  task_definition                   = var.modx_task_definition_arn
  desired_count                     = var.modx_desired_count
  health_check_grace_period_seconds = 60
  scheduling_strategy               = "REPLICA"
  enable_ecs_managed_tags           = true
  propagate_tags                    = "TASK_DEFINITION"
  enable_execute_command            = false

  capacity_provider_strategy {
    capacity_provider = aws_ecs_capacity_provider.ecs_asg.name
    weight            = 1
    base              = 0
  }

  load_balancer {
    target_group_arn = var.modx_target_group_arn
    container_name   = "modx-nginx"
    container_port   = 80
  }

  deployment_circuit_breaker {
    enable   = true
    rollback = true
  }

  ordered_placement_strategy {
    type  = "spread"
    field = "attribute:ecs.availability-zone"
  }

  ordered_placement_strategy {
    type  = "spread"
    field = "instanceId"
  }

  tags = {
    Name = "${var.environment}-${var.project_name}-service"
  }
}
```

### Configuration components

* Each service need a cluster to register itself with. Thats why we need the parameter `cluster`
* A service is used to run the container that are defined in `task-definition`
* How much instance of this service do i need to be running, at anytime? Used to handle high traffic demands

{% hint style="info" %}
**desired\_count = var.modx\_desired\_count**

How many task copies you want running simultaneously. If you set this to 3, ECS tries to maintain 3 running tasks at all times. If one dies, ECS spins up a replacement.

**Scenario**: Your MODX app gets traffic spikes during business hours. You might set desired\_count=5 during day, scale down to 2 at night.
{% endhint %}

* When a service is up and running, the ECS tries to check the health check of ALB, to see if the service is running fine or not. The interval at which this service checks the health checks, is controlled by `health_check_grace_period_seconds`\
  &#x20;

{% hint style="info" %}
How long ECS waits before it starts checking ALB health checks after a task starts.\
\
**Scenario**: Your MODX container takes 45 seconds to boot up (nginx starts, PHP-FPM initializes, MODX connects to RDS). Without this grace period, ALB would mark the task unhealthy while it's still booting, causing ECS to kill and restart it repeatedly. Setting 60 seconds gives it breathing room.
{% endhint %}

* Propagating the tags that you mentioned in the task-defintion, onto the running instance of the service, is handled by parameter `enable_ecs_managed_tags`  and `propagate_tags`

{% hint style="info" %}
ECS automatically adds tags to your tasks (like cluster name, service name).

**propagate\_tags = "TASK\_DEFINITION"** Takes tags from your task definition and copies them to running tasks.

**Scenario**: You tag your task definition with `Cost-Center=Marketing` and `Team=Web`. With propagation enabled, every running task inherits these tags. Now your cost reports can break down spend by team without manual tagging of each task.
{% endhint %}

* If you want to run some command inside the running instance of the service ( or EC2 over which the service is running its containers ) can be done via parameter `enable_execute_command` \
  This is sort of doing **SSH** into the EC2

{% hint style="info" %}
This controls ECS Exec\
basically SSH-like access into running containers.

**Scenario**: If true, you could do `aws ecs execute-command` to get a shell inside your running MODX container for debugging. You've disabled it, probably for security (it requires additional IAM permissions and CloudWatch logging setup).
{% endhint %}

* To control the underlying infra, where the service will be running, we use `capacity_provider_strategy` .&#x20;

```terraform
capacity_provider_strategy {
  capacity_provider = aws_ecs_capacity_provider.ecs_asg.name
  weight            = 1
  base              = 0
}
```

{% hint style="info" %}
This ties your service to an Auto Scaling Group via a capacity provider.

* **weight = 1**: If you had multiple capacity providers (say, Spot instances + On-Demand), weight determines the ratio. Weight=1 means 100% goes here since it's the only one.
* **base = 0**: Minimum tasks to run on this provider before considering others. Since you only have one provider, this doesn't matter much.

**Scenario**: Imagine you had two capacity providers—Spot (weight=3) and On-Demand (weight=1, base=2). ECS would:

1. Put first 2 tasks on On-Demand (base)
2. Split remaining tasks 3:1 ratio (Spot:On-Demand)

For 10 tasks: 2 on On-Demand (base), then 6 on Spot and 2 on On-Demand from the remaining 8.
{% endhint %}

* If the service is exposed via ALB, then we need to define the target group ARN, where the service will be handling its traffic ( exposing its container ). This is controlled via parameter `target_group_arn`

{% hint style="info" %}
**container\_name = "modx-nginx"**: Must match the name in your task definition. If your task def has containers named "nginx" and "php-fpm", you specify which one receives ALB traffic.

**container\_port = 80**: The port nginx listens on inside the container.
{% endhint %}

* If the service deployment fails, then we need to rollback to the last stable version of the service. This is controlled via parameter `deployment_circuit_breaker`
* Ordered placement strategy is used to control the distrubution/deployment of service, over the EC2 instance, that are registered via the **capacity provider**

```hcl
ordered_placement_strategy {
  type  = "spread"
  field = "attribute:ecs.availability-zone"
}

ordered_placement_strategy {
  type  = "spread"
  field = "instanceId"
}
```

{% hint style="info" %}
Controls how ECS distributes tasks across your EC2 instances.

**First strategy**: Spread tasks across AZs (us-east-1a, us-east-1b, us-east-1c).\
**Second strategy**: Within each AZ, spread across different EC2 instances.

**Scenario**: You have 6 tasks and 3 instances spread across 2 AZs:

* AZ-A: instance-1, instance-2
* AZ-B: instance-3

ECS places:

1. 3 tasks in AZ-A, 3 in AZ-B (AZ spread)
2. Within AZ-A: tasks split between instance-1 and instance-2 (instance spread)
3. Within AZ-B: all 3 tasks on instance-3 (only one instance there)

This protects against AZ failures and prevents all tasks from landing on one instance.
{% endhint %}

