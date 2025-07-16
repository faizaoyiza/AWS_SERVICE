
# Mini Project - Terraform EC2 Instance and AMI Creation

## Objectives
- Learn how to write basic Terraform configuration files.
- Automate creation of an EC2 instance on AWS using Terraform.
- Automate creation of an AMI from the EC2 instance using Terraform.

## Prerequisites
- AWS Account with required IAM permissions.
- AWS CLI installed and configured locally.
- Terraform installed on your computer.

## Task 1 - Confirm the Prerequisites

1. **Login** to your AWS Account to confirm it is functional.
2. Run `aws --version` to confirm AWS CLI is installed.
3. Run `aws configure list` to check CLI configuration.
4. Run `aws sts get-caller-identity` to confirm authentication.
5. Run `terraform -version` to confirm Terraform is installed.
<img width="950" height="461" alt="Image" src="https://github.com/user-attachments/assets/c3eba441-e0d5-4e6e-9f7f-fa793394fa20" />
## Task 2 - Terraform Script: EC2 and AMI

### Create Project Directory

```bash
mkdir terraform-ec2-ami
cd terraform-ec2-ami
```
### Sample `main.tf`

```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "my_ec2_instance" {
  ami           = "ami-0c55b159cbfafe1fe"
  instance_type = "t2.micro"

  tags = {
    Name = "MyEC2Instance"
  }
}

resource "aws_ami_from_instance" "my_ami" {
  name               = "MyCustomAMI"
  source_instance_id = aws_instance.my_ec2_instance.id
  depends_on         = [aws_instance.my_ec2_instance]
}
```
<img width="701" height="499" alt="Image" src="https://github.com/user-attachments/assets/47bee40c-8192-4c46-b83e-9329de1355d0" />


## Task 3 - Executing the Terraform Script

1. Initialize the project: `terraform init`
2. Validate configuration: `terraform validate`
3. Preview actions: `terraform plan`
4. Apply the configuration: `terraform apply`
<img width="683" height="404" alt="Image" src="https://github.com/user-attachments/assets/7b0b8084-a27c-46bb-b956-8a489e187e2b" />
<img width="683" height="447" alt="Image" src="https://github.com/user-attachments/assets/27eb88b5-7802-4d68-996c-0a664fd424f2" />

## Task 4 - Confirm Resources

- Login to AWS Console
- Check EC2 Instances for "MyEC2Instance"
- Check AMIs for "MyCustomAMI"
<img width="944" height="459" alt="Image" src="https://github.com/user-attachments/assets/48e31a3c-3721-4d1e-afed-c4bc22c0e789" />
## Task 5 - Clean Up

```bash
terraform destroy
```
<img width="673" height="140" alt="Image" src="https://github.com/user-attachments/assets/07f3dbb8-2d2a-4fd9-a1bd-eee3977dc218" />

## Documentation

- Note any errors encountered during `terraform apply`.
- Document how resources appeared in AWS Console.
- Reflect on how infrastructure as code improves automation.
