---
description: >-
  This page contain the details about how the repository for QuickSpin is
  structured
icon: github
---

# Repo Structure

### Insert comment in Python

We use three \` to insert comments in Python

````
```
Converts quickspin.yml to terraform.tfvars.json format 
```
````

### quickspin.yml

```
networking:
  user_ip: "xx.36.144.xxx"

instances:
  # ami          : "" # "ami-0360c520857e3138f"
  # instance_type: "" # possible values are t2.micro, t2.small, t2.medium, t2.large, t2.xlarge, t2.2xlarge
  # is_public    : "" # possible values are true or false

  - ami: "ami-0360c520857e3138f"
    instance_type: "t2.small"
    is_public: true

  - ami: "ami-0360c520857e3138f"
    instance_type: "t2.micro"
    is_public: false
```
