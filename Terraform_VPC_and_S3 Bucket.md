# Mini Project: Terraform Modules – VPC and S3 Bucket with Remote Backend Storage

## Introduction

In this mini project, I used **Terraform Modules** to provision AWS infrastructure in a reusable and organized manner. Rather than placing all resources in a single configuration file, I separated the infrastructure into independent modules for creating an Amazon VPC and an Amazon S3 bucket.

To improve collaboration and state management, I also configured **Amazon S3** as the remote backend for storing the Terraform state file and **Amazon DynamoDB** for state locking. This approach follows Terraform best practices by improving modularity, maintainability, and preventing state conflicts when multiple users work on the same infrastructure.

---

# Project Objectives

The objectives of this project were to:

- Create reusable Terraform modules.
- Build an Amazon VPC using a Terraform module.
- Build an Amazon S3 bucket using a Terraform module.
- Configure Terraform Remote Backend using Amazon S3.
- Enable state locking using Amazon DynamoDB.
- Deploy the infrastructure using Terraform commands.

---

# Prerequisites

Before beginning this project, I ensured the following tools and services were available:

- AWS Account
- AWS CLI installed and configured
- Terraform installed
- Visual Studio Code
- Git (optional)

AWS credentials were configured using:

```bash
aws configure
```

---

# Creating the Project Structure

I created a new Terraform project directory.

```bash
mkdir terraform-modules-vpc-s3
```

Then navigated into the directory.

```bash
cd terraform-modules-vpc-s3
```

Next, I created separate folders for the Terraform modules.

```bash
mkdir -p modules/vpc
mkdir -p modules/s3
```

The completed project structure looked like this:

```text
terraform-modules-vpc-s3/
│
├── backend.tf
├── main.tf
├── outputs.tf
├── variables.tf
│
└── modules/
    ├── vpc/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf
    │
    └── s3/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

Organizing the project this way makes the infrastructure easier to maintain and reuse across multiple projects.

---

# Creating the VPC Module

The VPC module was created inside:

```text
modules/vpc/
```

The module consisted of three files:

- `main.tf`
- `variables.tf`
- `outputs.tf`

## Variables

The VPC module accepts customizable variables such as:

- VPC Name
- CIDR Block
- Public Subnet CIDR
- Availability Zone

Example:

```hcl
variable "vpc_cidr" {
  type = string
}

variable "public_subnet_cidr" {
  type = string
}
```

Using variables makes the module reusable without modifying the source code.

---

## Resources

The VPC module provisions:

- Amazon VPC
- Public Subnet
- Internet Gateway
- Route Table
- Route Table Association

Example:

```hcl
resource "aws_vpc" "main" {

  cidr_block = var.vpc_cidr

  tags = {
    Name = "Terraform-VPC"
  }

}
```

---

## Outputs

The module exports important values.

Example:

```hcl
output "vpc_id" {

  value = aws_vpc.main.id

}
```

Outputs allow other modules to reference the VPC after deployment.

---

# Creating the S3 Module

The S3 module was created inside:

```text
modules/s3/
```

Like the VPC module, it contains:

- `main.tf`
- `variables.tf`
- `outputs.tf`

---

## Variables

The S3 module accepts configurable values such as:

- Bucket Name
- Bucket Tags

Example:

```hcl
variable "bucket_name" {

  type = string

}
```

---

## Resources

The module provisions an Amazon S3 bucket.

Example:

```hcl
resource "aws_s3_bucket" "bucket" {

  bucket = var.bucket_name

}
```

This allows different projects to create unique buckets simply by changing the variable value.

---

## Outputs

The S3 module exports the bucket name.

Example:

```hcl
output "bucket_name" {

  value = aws_s3_bucket.bucket.bucket

}
```

---

# Using the Modules

The root `main.tf` file calls both modules.

Example:

```hcl
module "vpc" {

  source = "./modules/vpc"

  vpc_cidr = "10.0.0.0/16"

  public_subnet_cidr = "10.0.1.0/24"

}

module "s3" {

  source = "./modules/s3"

  bucket_name = "my-unique-terraform-bucket"

}
```

Using modules keeps the project organized and eliminates duplicated code.

---

# Configuring Remote Backend

One of the most important parts of this project was configuring Terraform Remote Backend.

Instead of storing the Terraform state file locally, it was stored securely inside an Amazon S3 bucket.

The backend configuration was placed inside:

```text
backend.tf
```

Example:

```hcl
terraform {

  backend "s3" {

    bucket = "terraform-state-bucket"

    key = "terraform.tfstate"

    region = "us-east-1"

    dynamodb_table = "terraform-lock-table"

  }

}
```

Using a remote backend allows multiple team members to safely manage the same infrastructure.

---

# Configuring DynamoDB State Locking

To prevent multiple users from modifying the infrastructure simultaneously, Terraform state locking was enabled using Amazon DynamoDB.

The DynamoDB table included:

- Table Name

```text
terraform-lock-table
```

- Partition Key

```text
LockID
```

- Data Type

```text
String
```

State locking prevents corruption of the Terraform state file during concurrent deployments.

---

# Initializing Terraform

After completing the configuration, I initialized Terraform.

```bash
terraform init -reconfigure
```

The `-reconfigure` option ensured Terraform updated the backend configuration correctly.

---

# Validating the Configuration

Next, I validated the configuration.

```bash
terraform validate
```

Terraform confirmed that all configuration files were valid.

---

# Reviewing the Execution Plan

Before provisioning resources, I generated an execution plan.

```bash
terraform plan
```

This displayed the resources Terraform intended to create.

---

# Deploying the Infrastructure

After reviewing the plan, I provisioned the infrastructure.

```bash
terraform apply
```

Terraform successfully created:

- Amazon VPC
- Public Subnet
- Internet Gateway
- Route Table
- Amazon S3 Bucket

The Terraform state file was automatically stored in the configured S3 backend.

---

# Resources Created

The deployment successfully provisioned the following AWS resources:

- Amazon VPC
- Public Subnet
- Internet Gateway
- Route Table
- Route Table Association
- Amazon S3 Bucket
- Remote Terraform State in S3
- DynamoDB State Lock

---

# Verifying the Deployment

After deployment, I verified that:

- The VPC appeared in the AWS VPC Console.
- The public subnet was created successfully.
- The S3 bucket existed with the specified name.
- The Terraform state file (`terraform.tfstate`) was stored inside the backend bucket.
- DynamoDB contained the lock information during Terraform operations.

These checks confirmed that both the infrastructure and backend configuration were functioning correctly.

---

# Observations

Throughout the project, I observed that:

- Terraform modules significantly improve code organization.
- Modules make infrastructure reusable across different projects.
- Remote state storage allows teams to collaborate safely.
- DynamoDB locking prevents state corruption.
- Outputs simplify communication between modules.
- Terraform automatically manages resource dependencies.

---

# Challenges Encountered

During implementation, I encountered a few challenges.

### Bucket Name Already Exists

Since Amazon S3 bucket names are globally unique, Terraform failed when a duplicate name was used.

**Solution**

Used a unique bucket name.

---

### Backend Initialization Errors

Terraform failed to initialize when the backend bucket did not exist.

**Solution**

Created the backend S3 bucket and DynamoDB table before running:

```bash
terraform init -reconfigure
```

---

### State Lock Errors

Terraform occasionally reported that the state was locked.

**Solution**

Verified that the DynamoDB table was configured correctly and ensured no other Terraform process was running.

---

# Key Concepts Learned

Through this project, I learned how to:

- Build reusable Terraform modules.
- Separate infrastructure into logical components.
- Create an Amazon VPC using Terraform.
- Provision an Amazon S3 bucket.
- Configure a remote backend using Amazon S3.
- Enable state locking using DynamoDB.
- Pass variables into Terraform modules.
- Export outputs from Terraform modules.
- Initialize, validate, plan, and apply Terraform configurations.
- Manage infrastructure following Infrastructure as Code (IaC) best practices.

---

# Conclusion

This mini project provided practical experience with Terraform Modules and remote state management. By separating the infrastructure into dedicated VPC and S3 modules, I created a cleaner, reusable, and more maintainable Terraform project. I also configured Amazon S3 as the remote backend and Amazon DynamoDB for state locking, ensuring secure and reliable state management during deployments.

Overall, this project reinforced the importance of modular Infrastructure as Code, demonstrated best practices for collaborative Terraform development, and provided hands-on experience managing AWS infrastructure using reusable modules and remote backend storage.
