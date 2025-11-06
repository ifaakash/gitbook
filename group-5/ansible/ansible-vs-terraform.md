# Ansible vs Terraform

| **Feature**      | **Terraform**                                                                                                                             | **Ansible**                                                                                                                                 |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| Primary Use      | Infrastructure Provisioning (Day 0) - Creating, modifying, and destroying resources like VMs, networks, databases, load balancers, etc.   | Configuration Management (Day 1+) - Installing software, managing services, updating files, and deploying applications on existing systems. |
| Approach         | Declarative (You declare the _desired end-state_, and Terraform figures out _how_ to get there).                                          | Procedural/Imperative (You define the _steps_—the "how"—and Ansible executes them sequentially).                                            |
| Language         | HCL (HashiCorp Configuration Language) - designed for IaC.                                                                                | YAML (for Playbooks) - human-readable, simple syntax.                                                                                       |
| State Management | Stateful - It maintains a state file (`.tfstate`) to track the current state of the infrastructure it manages, making it lifecycle-aware. | Stateless - It does not maintain a state file of managed resources. It determines the current state by directly connecting to the system.   |
| Mechanism        | Communicates with Cloud Provider APIs (AWS, Azure, GCP, etc.) to provision and manage resources.                                          | Communicates with managed hosts, typically over SSH (Linux/Unix) or WinRM (Windows), to execute tasks. It is agentless.                     |

### How They Work Together (The Combined Power)

In modern DevOps practices, it's very common and highly recommended to use both tools in sequence because their strengths are complementary:

Terraform (Provisioning): You use Terraform to create the core infrastructure, like virtual machines, a VPC network, and a database instance on a cloud provider. This is your foundation.

```
Example: Creating 5 EC2 instances.
```

Ansible (Configuration): Once the infrastructure is created, you use Ansible to connect to those newly provisioned resources (the 5 EC2 instances) and configure them.

```
Example: Installing Nginx, configuring firewall rules, setting up user accounts, and deploying your application code onto those 5 EC2 instances.
```

This separation of concerns makes your automation pipeline cleaner, more robust, and more scalable.
