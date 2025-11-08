---
description: >-
  This page describe the working of Session Manager and how to use it, for
  establishing a complete isolate and private network for connection to EC2
  instance
icon: aws
---

# How does SSM Works in AWS?

## What is SSM?

**AWS Systems Manager (SSM)** is a fully managed AWS service that helps you **securely manage and automate operational tasks** across your AWS resources. A popular feature of SSM is **Session Manager**, which allows you to **connect to EC2 instances securely** – without opening inbound ports, creating SSH keys, or setting up a bastion host.

## How does SSM is helpful?

The traditional method for connecting to private instance be like creating a bastion host, and then using it to connect with the Private instance ( considering the instance is reachable from internet, via use of **NAT Gateway,** and the security group of private instance **allow the bastion instance, in inbound rules** )

Also, we tend to use the **key pair** (kp) to connect with the instance. Below is an example of that way:

```
ssh -i kp.pem ubuntu@<public-ip>
```

With Session Manager, you can connect using a single command:

```
aws ssm start-session --target <instance-id>
```

## Benefit of Using session manager?

* No need for SSH keys or Bastion Host
* No open ports ( no inbound traffic in SG )
* No need to attach a public IP ( works in private subnet )
* Session logs can be stored in Cloudwatch
* IAM based access control ( no intern can touch it! )

## How SSM works?

On a high level idea, Each EC2 instance must have the **SSM Agent** installed, for the Session Manager to work.

This SSM agent is responsible for querying over a network for any request of connection. If this SSM agent found any request, then the agent open the tunnel ( which is routes in AWS native network ) and then let the user connect to the Instance. The whole traffic flows withing the AWS network and thus, is the most secured way of connection to any instance.&#x20;

The SSM agent sends outbound traffic to the SSM endpoint ( which has DNS enabled ) so that any traffic to SSM endpoint ( com.ssm.\<region>.aamzonaws.com ) is resolved to the internal domain name for SSM ( ssm.amazonaws.com ). Thus, the SSM agent is able to reach out to SSM endpoint locally, and so it dont go to internet, to find the SSM endpoint.&#x20;

If your instance is in a private subnet, you can create **VPC Endpoints** for SSM.\
This ensures that the agent communicates privately via the AWS internal network instead of the internet.

## What are pre-requisites for SSM?

You’ll need:

* An **EC2 instance** (preferably in a private subnet)
* An **IAM role** with the managed policy:\
  &#xNAN;**`AmazonSSMManagedInstanceCore`**
* **VPC Endpoints** for:
  * `com.amazonaws.<region>.ssm`
  * `com.amazonaws.<region>.ssmmessages`
  * `com.amazonaws.<region>.ec2messages`
* AWS Console or CLI access

## How to create

### The IAM role for SSM

We will be creating an IAM role.&#x20;

1. Go to **IAM → Roles → Create role**
2. Select **EC2** as the trusted entity
3. Attach the policy
   1. The policy will be selected for you already ( named _**AmazonSSMManagedInstanceCore**_ )
4. Click **Create role** and attach it to your EC2 instance

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>



> #### What this policy does:
>
> This **AWS-managed policy** allows your EC2 instance to:
>
> * Register itself as a **managed instance** with Systems Manager
> * Use **Session Manager**, **Run Command**, and **Patch Manager**
> * Access **SSM messages**, **SSM agent**, and **CloudWatch logs**
>
> Internally, it grants permissions to services like:
>
> * `ssm:UpdateInstanceInformation`
> * `ssmmessages:CreateControlChannel`
> * `ec2messages:AcknowledgeMessage`
> * and others required by the SSM Agent

Below is the code snippet, to create the IAM role via _Terraform_&#x20;

{% code title="iam.tf" lineNumbers="true" %}
```yaml
# Create the IAM Role for EC2 Instance
resource "aws_iam_role" "iam" {
  name = var.role_name
  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Principal = {
          Service = "ec2.amazonaws.com"
        }
      }
    ]
  })
}

# Attach Managed Instance Core Policy to IAM Role
resource "aws_iam_role_policy_attachment" "iam_policy" {
  policy_arn = "arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore"
  role       = aws_iam_role.iam.name
}

# Create the IAM Instance Profile
resource "aws_iam_instance_profile" "instance_profile" {
  name = var.instance_profile_name
  role = aws_iam_role.iam.name
}

```
{% endcode %}

### VPC Endpoints for SSM

* `com.amazonaws.<region>.ssm`
* `com.amazonaws.<region>.ssmmessages`
* `com.amazonaws.<region>.ec2messages`

> The SSM agent send the outbound traffic to the SSM endpoints, to notify if there is a requst for the SSM connection. Once the user click on "**Connect"** button, then the SSM agent send request to the VPC endpoinst for SSM ( using the IAM policy for SSM ) via the secure AWS tunnel, and thus the endpoints helps to open a shell based session, helping to login the EC2 Instance

Below is the code snippet for VPC endpoints for session manager via **Terraform**

{% code title="vpc-endpoints.tf" %}
```yaml
resource "aws_security_group" "endpoint_sg" {
  name        = "${var.prefix}-vpc-endpoints-sg"
  description = "Security group for VPC endpoints"
  vpc_id      = aws_vpc.main.id
  tags        = merge({ "Name" : "${var.prefix}-sg" }, var.default_tags)
}

resource "aws_vpc_security_group_ingress_rule" "endpoints_ingress" {
  from_port                    = 443
  to_port                      = 443
  ip_protocol                  = "tcp"
  security_group_id            = aws_security_group.endpoint_sg.id # Allow traffic from the Instance security group
  referenced_security_group_id = aws_security_group.main.id
}

resource "aws_vpc_security_group_egress_rule" "endpoints_egress" {
  security_group_id = aws_security_group.endpoint_sg.id
  ip_protocol       = "-1" # semantically equivalent to all ports
  cidr_ipv4         = "0.0.0.0/0"
}

resource "aws_vpc_endpoint" "ssm" {
  vpc_id              = aws_vpc.main.id
  service_name        = "com.amazonaws.us-east-1.ssm"
  vpc_endpoint_type   = "Interface"
  security_group_ids  = [aws_security_group.endpoint_sg.id]
  subnet_ids          = [aws_subnet.private.id]
  private_dns_enabled = true
}

resource "aws_vpc_endpoint" "ssm_messages" {
  vpc_id              = aws_vpc.main.id
  service_name        = "com.amazonaws.us-east-1.ssmmessages"
  vpc_endpoint_type   = "Interface"
  security_group_ids  = [aws_security_group.endpoint_sg.id]
  subnet_ids          = [aws_subnet.private.id]
  private_dns_enabled = true
}

resource "aws_vpc_endpoint" "ec2_messages" {
  vpc_id              = aws_vpc.main.id
  service_name        = "com.amazonaws.us-east-1.ec2messages"
  vpc_endpoint_type   = "Interface"
  security_group_ids  = [aws_security_group.endpoint_sg.id]
  subnet_ids          = [aws_subnet.private.id]
  private_dns_enabled = true
}

```
{% endcode %}

### Security Group Configuration

* **EC2 Instance:**\
  No inbound rules are required.
* **VPC Endpoints:**\
  Must allow **inbound HTTPS (443)** traffic from the EC2 instance’s security group.

This allows the SSM Agent to send outbound HTTPS traffic to the endpoint, initiating the secure session tunnel.
