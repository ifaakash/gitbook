# Week 1-2 \[ till 20th Sept ]

## Two-Week DevOps Fundamentals Plan

### Week 1: Git, CI/CD, and Introduction to IaC

#### Day 1: Git Basics

* Theory:
  * What is version control?
  * Git fundamentals: repositories, commits, branches
* Hands-on:
  * Install Git
  * Configure Git (username, email)
  * Create a local repository
  * Make commits
* Exercise: Create a simple "Hello World" program and track changes using Git

#### Day 2: Advanced Git

* Theory:
  * Branching strategies (Git Flow, GitHub Flow)
  * Merging and resolving conflicts
* Hands-on:
  * Create and switch branches
  * Merge branches
  * Resolve a merge conflict
* Exercise: Collaborate on a small project with a friend using Git (e.g., a simple calculator)

#### Day 3: CI/CD Introduction

* Theory:
  * What is CI/CD?
  * Benefits of CI/CD
  * Popular CI/CD tools (Jenkins, GitLab CI, GitHub Actions)
* Hands-on:
  * Set up a GitHub account
  * Create a repository on GitHub
  * Push local repository to GitHub
* Exercise: Write a simple "Hello World" program in Python and create a GitHub Actions workflow to run tests

#### Day 4: Advanced CI/CD

* Theory:
  * Continuous Integration best practices
  * Continuous Delivery vs. Continuous Deployment
* Hands-on:
  * Expand your GitHub Actions workflow
  * Implement automated testing
  * Set up automatic deployment to a staging environment
* Exercise: Create a CI/CD pipeline for a simple web application (e.g., a Flask app)

#### Day 5: Introduction to Infrastructure as Code

* Theory:
  * What is Infrastructure as Code?
  * Benefits of IaC
  * Popular IaC tools (Terraform, AWS CloudFormation, Ansible)
* Hands-on:
  * Install Terraform
  * Write a simple Terraform configuration to create an AWS S3 bucket
* Exercise: Use Terraform to create a basic AWS VPC with public and private subnets

#### Day 6-7: Weekend Project

* Create a small web application (e.g., a todo list)
* Set up a Git repository for the project
* Implement a CI/CD pipeline using GitHub Actions
* Use Terraform to provision the necessary infrastructure on AWS or another cloud provider

### Week 2: Advanced IaC and Docker

#### Day 8: Advanced Infrastructure as Code

* Theory:
  * Terraform state management
  * Terraform modules
* Hands-on:
  * Create a Terraform module for a reusable infrastructure component
  * Use remote state storage for your Terraform configuration
* Exercise: Refactor your weekend project's Terraform code to use modules

#### Day 9: Docker Basics

* Theory:
  * What is containerization?
  * Docker architecture
  * Containers vs. Virtual Machines
* Hands-on:
  * Install Docker
  * Pull and run your first Docker container
  * Write a simple Dockerfile
* Exercise: Containerize the web application from your weekend project

#### Day 10: Advanced Docker

* Theory:
  * Docker networking
  * Docker volumes
  * Docker Compose
* Hands-on:
  * Create a multi-container application using Docker Compose
  * Implement persistent storage using Docker volumes
* Exercise: Extend your web application to use a separate database container

#### Day 11: CI/CD with Docker

* Theory:
  * Container registries
  * Integrating Docker into CI/CD pipelines
* Hands-on:
  * Set up a container registry (e.g., Docker Hub or AWS ECR)
  * Update your CI/CD pipeline to build and push Docker images
* Exercise: Implement a complete CI/CD pipeline that builds, tests, and deploys your containerized application

#### Day 12: Infrastructure as Code for Container Orchestration

* Theory:
  * Introduction to Kubernetes
  * Using Terraform with Kubernetes
* Hands-on:
  * Write Terraform code to set up a managed Kubernetes cluster (e.g., EKS on AWS)
  * Deploy your containerized application to Kubernetes using Terraform
* Exercise: Create a Terraform module for deploying applications to Kubernetes

#### Day 13-14: Final Weekend Project

* Integrate all the concepts learned over the two weeks:
  * Create a microservices-based application (e.g., e-commerce site with separate services for users, products, and orders)
  * Use Git for version control with a proper branching strategy
  * Implement a comprehensive CI/CD pipeline using GitHub Actions
  * Use Terraform to provision all necessary infrastructure, including a Kubernetes cluster
  * Containerize all services using Docker
  * Deploy the application to Kubernetes
  * Implement monitoring and logging for your application

Throughout the two weeks:

* Read documentation for Git, GitHub Actions, Terraform, and Docker
* Follow DevOps blogs and news sources to stay updated on best practices
* Join DevOps communities (e.g., Reddit, Stack Overflow) to ask questions and learn from others
