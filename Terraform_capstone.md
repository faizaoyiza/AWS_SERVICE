# Terraform Capstone Project: Automated WordPress Deployment on AWS

## Project Overview

### Scenario

DigitalBoost, a digital marketing agency, requires a highly available, secure, and scalable WordPress hosting environment on AWS. As the AWS Solutions Architect, my responsibility was to automate the deployment of the complete infrastructure using **Terraform** following Infrastructure as Code (IaC) best practices.

The solution provisions a production-ready WordPress architecture consisting of a Virtual Private Cloud (VPC), public and private networking, NAT Gateway, Amazon RDS, Amazon EFS, Application Load Balancer (ALB), Auto Scaling Group (ASG), and EC2 instances running WordPress. The deployment emphasizes security, scalability, high availability, and automation.

---

# Project Objectives

The objectives of this capstone project were to:

- Automate AWS infrastructure provisioning using Terraform.
- Deploy WordPress on Amazon EC2 instances.
- Store the WordPress database on Amazon RDS (MySQL).
- Store WordPress uploads on Amazon EFS for shared access.
- Configure an Application Load Balancer (ALB).
- Implement Auto Scaling for fault tolerance and scalability.
- Secure the infrastructure using private subnets, security groups, and IAM roles.
- Demonstrate Infrastructure as Code (IaC) best practices.

---

# Architecture Overview

The deployed architecture consists of the following AWS services:

- Amazon VPC
- Public Subnets
- Private Subnets
- Internet Gateway
- NAT Gateway
- Route Tables
- Amazon RDS (MySQL)
- Amazon Elastic File System (EFS)
- Amazon EC2 Auto Scaling Group
- Application Load Balancer (ALB)
- Launch Template
- IAM Roles
- AWS Systems Manager (SSM)

The overall architecture provides a scalable, secure, and highly available WordPress deployment across multiple Availability Zones.

---

# Prerequisites

Before beginning the project, I ensured the following tools were installed and configured:

- AWS Account
- AWS CLI
- Terraform (v1.5 or later)
- Git
- Visual Studio Code

AWS credentials were configured using:

```bash
aws configure
```

Terraform version was verified with:

```bash
terraform version
```

AWS CLI version was verified with:

```bash
aws --version
```

---

# Project Directory Structure

The project was organized using reusable Terraform modules.

```text
terraform-wordpress/
│
├── versions.tf
├── provider.tf
├── variables.tf
├── outputs.tf
├── main.tf
│
├── user_data/
│   └── wordpress_bootstrap.sh
│
└── modules/
    ├── vpc/
    ├── nat/
    ├── rds/
    ├── efs/
    ├── alb/
    └── asg/
```

This modular design makes the infrastructure easier to maintain, update, and reuse.

---

# Security Architecture

Several security best practices were implemented throughout the project.

## Network Isolation

The network was divided into public and private subnets.

### Public Subnets

Hosted:

- Application Load Balancer
- NAT Gateway

### Private Subnets

Hosted:

- EC2 WordPress Instances
- Amazon RDS
- Amazon EFS

The EC2 instances and RDS database do not have public IP addresses.

---

## Security Groups

Separate security groups were created for each service.

### Application Load Balancer

Allowed:

- HTTP (80)
- HTTPS (443)

Inbound traffic originates from the internet.

---

### EC2 Instances

Allowed:

- HTTP (80) only from the ALB Security Group

No inbound SSH access from the internet.

---

### Amazon RDS

Allowed:

- MySQL (3306) only from the EC2 Security Group.

---

### Amazon EFS

Allowed:

- NFS (2049) only from the EC2 Security Group.

---

## IAM Security

Instead of SSH, EC2 instances use:

- AWS Systems Manager (SSM)

The EC2 IAM Role includes:

```text
AmazonSSMManagedInstanceCore
```

This removes the need to expose SSH to the internet.

---

## Secrets Management

Database credentials are stored securely using:

- AWS Systems Manager Parameter Store (SecureString)

or

- AWS Secrets Manager

Sensitive information is never hardcoded into Terraform configuration files.

---

# Module 1: Virtual Private Cloud (VPC)

The VPC module provisions the networking layer.

Resources created include:

- VPC
- Public Subnets
- Private Subnets
- Internet Gateway
- Route Tables
- Route Table Associations

The VPC CIDR block and subnet ranges are configurable using Terraform variables.

---

# Module 2: NAT Gateway

The NAT module provisions:

- Elastic IP
- NAT Gateway

The NAT Gateway enables outbound internet access for resources inside private subnets without exposing them directly to the internet.

---

# Module 3: Amazon RDS (MySQL)

The RDS module provisions:

- Amazon RDS MySQL Instance
- Database Subnet Group
- Security Group

Configuration includes:

- Multi-AZ deployment
- Automated backups
- Private subnet placement
- Secure database access

The WordPress application connects to this managed database.

---

# Module 4: Amazon Elastic File System (EFS)

The EFS module provisions:

- Amazon EFS File System
- Mount Targets
- Security Group

EFS provides shared storage for WordPress uploads.

Because all EC2 instances mount the same EFS volume, uploaded files remain synchronized across every instance in the Auto Scaling Group.

---

# Module 5: Application Load Balancer (ALB)

The ALB module provisions:

- Application Load Balancer
- Target Group
- HTTP Listener

Responsibilities include:

- Distributing traffic across multiple EC2 instances
- Health checking
- High availability

HTTPS can be enabled by attaching an ACM certificate.

---

# Module 6: Auto Scaling Group (ASG)

The ASG module provisions:

- Launch Template
- Auto Scaling Group
- Scaling Policies

Each EC2 instance automatically:

- Installs Apache
- Installs PHP
- Downloads WordPress
- Mounts Amazon EFS
- Connects to Amazon RDS

This configuration is performed using a User Data bootstrap script.

---

# WordPress Bootstrap Script

A User Data script named:

```text
wordpress_bootstrap.sh
```

automates the server configuration during instance launch.

The script performs the following tasks:

- Updates the operating system
- Installs Apache
- Installs PHP
- Installs MySQL client
- Downloads WordPress
- Mounts the EFS file system
- Configures Apache
- Connects WordPress to Amazon RDS
- Starts required services

Environment variables are injected into the Launch Template to provide:

- Database Host
- Database Name
- Database Username
- Database Password
- Amazon EFS DNS Name

This enables each EC2 instance to configure WordPress automatically during startup.

---

# Root Terraform Configuration

The root `main.tf` file calls each module.

Example:

```hcl
module "vpc" {
  source = "./modules/vpc"
}

module "nat" {
  source = "./modules/nat"
}

module "rds" {
  source = "./modules/rds"
}

module "efs" {
  source = "./modules/efs"
}

module "alb" {
  source = "./modules/alb"
}

module "asg" {
  source = "./modules/asg"
}
```

Terraform automatically provisions resources in the correct dependency order.

---

# Deploying the Infrastructure

## Initialize Terraform

```bash
terraform init
```

---

## Validate Configuration

```bash
terraform validate
```

Terraform confirms the configuration contains no syntax errors.

---

## Review the Execution Plan

```bash
terraform plan
```

This displays all resources that Terraform will create.

---

## Deploy Infrastructure

```bash
terraform apply
```

Terraform provisions:

- VPC
- Public and Private Subnets
- NAT Gateway
- Route Tables
- Amazon RDS
- Amazon EFS
- Application Load Balancer
- Launch Template
- Auto Scaling Group
- EC2 Instances
- IAM Roles
- Security Groups

---

# Viewing Terraform Outputs

After deployment:

```bash
terraform output
```

Typical outputs include:

- ALB DNS Name
- RDS Endpoint
- EFS DNS Name
- VPC ID

The ALB DNS name is used to access the WordPress website.

---

# Verifying the Deployment

After deployment, I opened the Application Load Balancer DNS name in a web browser.

The WordPress installation page loaded successfully, confirming:

- The Application Load Balancer was routing traffic correctly.
- EC2 instances were healthy.
- WordPress connected successfully to Amazon RDS.
- Amazon EFS provided shared storage for uploaded files.

---

# Demonstrating Auto Scaling

To verify scalability, I simulated increased application traffic.

As CPU utilization increased:

- Auto Scaling launched additional EC2 instances.
- New instances automatically mounted Amazon EFS.
- Each instance connected to the same Amazon RDS database.
- The ALB automatically registered healthy instances and distributed incoming requests.

This demonstrated the application's ability to scale automatically based on demand.

---

# Security Measures Implemented

The following security controls were implemented:

- Private subnets for EC2 and RDS
- No public IP addresses on application servers
- NAT Gateway for secure outbound internet access
- Security Groups implementing least privilege
- IAM Roles instead of access keys
- AWS Systems Manager replacing SSH
- Secure database credentials stored in Parameter Store or Secrets Manager
- Multi-AZ deployment for high availability
- Optional HTTPS support using AWS Certificate Manager (ACM)

---

# Cost Optimization

Several measures were implemented to reduce AWS costs:

- Single NAT Gateway for lab environments
- t3.micro EC2 instances
- db.t3.micro RDS instance
- Auto Scaling to launch instances only when required
- Amazon EFS lifecycle management
- Automated RDS backups

---

# Challenges Encountered

During implementation, I encountered several challenges.

### NAT Gateway Configuration

Private instances initially could not access the internet.

**Solution**

Verified route tables and associated the private subnet with the NAT Gateway.

---

### WordPress Database Connection

WordPress could not initially connect to Amazon RDS.

**Solution**

Updated the RDS Security Group to allow MySQL traffic only from the EC2 Security Group.

---

### EFS Mount Failure

Some EC2 instances failed to mount Amazon EFS during startup.

**Solution**

Verified mount targets existed in each Availability Zone and ensured NFS (Port 2049) was allowed.

---

### Auto Scaling Health Checks

New instances were marked unhealthy.

**Solution**

Adjusted the ALB health check path and confirmed Apache started successfully before health checks executed.

---

# Key Concepts Learned

Through this capstone project, I learned how to:

- Design highly available AWS architectures.
- Build reusable Terraform modules.
- Deploy secure networking using Amazon VPC.
- Configure public and private subnet architectures.
- Deploy managed databases using Amazon RDS.
- Implement shared storage using Amazon EFS.
- Configure Application Load Balancers.
- Implement Auto Scaling Groups.
- Automate WordPress installation using User Data.
- Secure infrastructure using IAM Roles and Systems Manager.
- Apply Infrastructure as Code (IaC) best practices.
- Deploy scalable cloud-native web applications on AWS.

---

# Conclusion

This Terraform Capstone Project demonstrated the complete deployment of a highly available, secure, and scalable WordPress environment on AWS. Using Terraform modules, I automated the provisioning of networking, storage, compute, database, and load balancing resources while following Infrastructure as Code best practices.

The deployment successfully integrated Amazon VPC, NAT Gateway, Amazon RDS, Amazon EFS, Application Load Balancer, Auto Scaling Group, and EC2 instances into a resilient WordPress hosting platform. The automated bootstrap process ensured that new EC2 instances could configure themselves without manual intervention, while Auto Scaling and the Application Load Balancer provided elasticity and fault tolerance. Overall, this project strengthened my understanding of cloud architecture, Terraform automation, and AWS best practices for deploying production-ready web applications.
