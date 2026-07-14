# Mini Project: Terraform EC2 Module and Security Group Module with Apache User Data

## Introduction

In this mini project, I used **Terraform** to provision an Amazon EC2 instance using a modular Infrastructure as Code (IaC) approach. Instead of defining all resources in a single configuration file, I separated the infrastructure into reusable modules for the EC2 instance and the Security Group.

To automate server configuration, I used a **User Data** script that installs and configures the Apache HTTP Server during instance launch. This eliminates manual setup after deployment and demonstrates how Terraform can automate both infrastructure provisioning and server configuration.

---

# Project Objectives

The objectives of this project were to:

- Create reusable Terraform modules.
- Provision an Amazon EC2 instance.
- Create a dedicated Security Group module.
- Configure inbound HTTP and SSH access.
- Use User Data to install and configure Apache.
- Verify that the web server is accessible through the EC2 public IP.
- Destroy the infrastructure after testing.

---

# Prerequisites

Before beginning the project, I installed and configured the following:

- AWS Account
- AWS CLI
- Terraform
- Visual Studio Code
- Git (optional)

AWS credentials were configured using:

```bash
aws configure
```

This prompted for:

- AWS Access Key ID
- AWS Secret Access Key
- Default Region
- Output Format

I also verified the installations.

```bash
aws --version
```

```bash
terraform --version
```

---

# Project Structure

I created a modular Terraform project with separate folders for the EC2 instance and Security Group.

```text
terraform-ec2-apache/
│
├── apache_userdata.sh
├── main.tf
├── providers.tf
├── variables.tf
├── outputs.tf
│
└── modules/
    ├── ec2/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf
    │
    └── security_group/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

This structure improves organization and allows each module to be reused in other Terraform projects.

---

# Creating the Project Directory

I created the project folder.

```bash
mkdir terraform-ec2-apache
```

Then navigated into it.

```bash
cd terraform-ec2-apache
```

Next, I created the module directories.

```bash
mkdir -p modules/ec2
```

```bash
mkdir -p modules/security_group
```

---

# Creating the EC2 Module

The EC2 module was created inside:

```text
modules/ec2/
```

The module contains:

- `main.tf`
- `variables.tf`
- `outputs.tf`

---

## EC2 Variables

The EC2 module accepts configurable values such as:

- AMI ID
- Instance Type
- Key Pair Name
- Subnet ID
- Security Group ID

Example:

```hcl
variable "instance_type" {
  type = string
}

variable "ami_id" {
  type = string
}
```

Using variables makes the module reusable across different environments.

---

## EC2 Resource

The module provisions an EC2 instance.

Example:

```hcl
resource "aws_instance" "web_server" {

  ami                    = var.ami_id

  instance_type          = var.instance_type

  key_name               = var.key_name

  subnet_id              = var.subnet_id

  vpc_security_group_ids = [var.security_group_id]

  user_data = file("${path.root}/apache_userdata.sh")

}
```

The EC2 instance automatically executes the User Data script during launch.

---

## EC2 Outputs

The module exports useful information.

Example:

```hcl
output "instance_public_ip" {

  value = aws_instance.web_server.public_ip

}
```

---

# Creating the Security Group Module

The Security Group module was created inside:

```text
modules/security_group/
```

The module contains:

- `main.tf`
- `variables.tf`
- `outputs.tf`

---

## Security Group Variables

Example:

```hcl
variable "vpc_id" {
  type = string
}
```

---

## Security Group Resource

The Security Group allows inbound SSH and HTTP traffic.

Example:

```hcl
resource "aws_security_group" "web_sg" {

  name = "apache-security-group"

  ingress {

    from_port = 22

    to_port = 22

    protocol = "tcp"

    cidr_blocks = ["0.0.0.0/0"]

  }

  ingress {

    from_port = 80

    to_port = 80

    protocol = "tcp"

    cidr_blocks = ["0.0.0.0/0"]

  }

  egress {

    from_port = 0

    to_port = 0

    protocol = "-1"

    cidr_blocks = ["0.0.0.0/0"]

  }

}
```

This configuration allows:

- SSH access (Port 22)
- HTTP access (Port 80)

---

## Security Group Outputs

Example:

```hcl
output "security_group_id" {

  value = aws_security_group.web_sg.id

}
```

The Security Group ID is passed to the EC2 module.

---

# Creating the Apache User Data Script

To automate software installation, I created a User Data script named:

```text
apache_userdata.sh
```

Example:

```bash
#!/bin/bash

yum update -y

yum install httpd -y

systemctl start httpd

systemctl enable httpd

echo "<h1>Hello World from Terraform!</h1>" > /var/www/html/index.html
```

The script performs the following tasks:

- Updates system packages.
- Installs Apache HTTP Server.
- Starts the Apache service.
- Enables Apache to start automatically after reboot.
- Creates a simple web page displaying **Hello World from Terraform!**

After creating the script, I made it executable.

```bash
chmod +x apache_userdata.sh
```

---

# Root Terraform Configuration

The root `main.tf` file connects both modules.

Example:

```hcl
module "security_group" {

  source = "./modules/security_group"

  vpc_id = var.vpc_id

}

module "ec2" {

  source = "./modules/ec2"

  ami_id = var.ami_id

  instance_type = var.instance_type

  key_name = var.key_name

  subnet_id = var.subnet_id

  security_group_id = module.security_group.security_group_id

}
```

This allows the EC2 module to use the Security Group created by the Security Group module.

---

# Initializing Terraform

I initialized the project.

```bash
terraform init
```

Terraform downloaded all required providers.

---

# Validating the Configuration

Next, I validated the configuration.

```bash
terraform validate
```

Terraform confirmed there were no syntax errors.

---

# Reviewing the Execution Plan

Before creating resources, I reviewed the execution plan.

```bash
terraform plan
```

Terraform displayed all resources that would be created.

---

# Deploying the Infrastructure

To provision the infrastructure, I executed:

```bash
terraform apply
```

Terraform successfully created:

- Amazon EC2 Instance
- Security Group

The User Data script automatically installed Apache during instance initialization.

---

# Verifying the Deployment

After deployment, I copied the EC2 public IP address from the AWS Console.

Example:

```text
http://<EC2-Public-IP>
```

When opened in a web browser, the following page appeared:

```text
Hello World from Terraform!
```

This confirmed that:

- The EC2 instance launched successfully.
- The User Data script executed correctly.
- Apache HTTP Server was installed automatically.
- The Security Group allowed HTTP traffic.
- The web server was functioning properly.

---

# Troubleshooting

## Website Not Accessible

Possible causes:

- Security Group does not allow Port 80.
- Instance is not in a public subnet.
- No Internet Gateway attached.

**Solution**

Verify:

- Port 80 is open.
- Public IP is assigned.
- Route table includes an Internet Gateway.

---

## User Data Did Not Execute

Check the cloud-init logs.

```bash
/var/log/cloud-init.log
```

```bash
/var/log/cloud-init-output.log
```

Also verify:

```hcl
user_data = file("${path.root}/apache_userdata.sh")
```

---

## AMI Not Found

Some AMIs are region-specific.

**Solution**

Use the correct Amazon Linux or Ubuntu AMI for your AWS region.

---

## SSH Connection Failed

Verify:

- Key pair is correct.
- Security Group allows Port 22.
- EC2 instance has a public IP.

---

# Cleaning Up Resources

After completing the project, I removed all AWS resources.

```bash
terraform destroy
```

Terraform deleted:

- EC2 Instance
- Security Group

This prevented unnecessary AWS charges.

---

# Observations

During the project, I observed that:

- Terraform modules improve code organization.
- User Data eliminates manual server configuration.
- Security Groups provide controlled access to cloud resources.
- Outputs enable communication between modules.
- Terraform automatically manages resource dependencies.
- Infrastructure can be recreated consistently using the same configuration files.

---

# Challenges Encountered

Some challenges included:

- Selecting the correct AMI for the deployment region.
- Ensuring the Security Group allowed inbound HTTP traffic.
- Waiting for the User Data script to complete before testing the website.
- Verifying Apache installation through cloud-init logs when troubleshooting.

Each issue was resolved by reviewing Terraform outputs, checking the AWS EC2 Console, and inspecting the cloud-init logs.

---

# Key Concepts Learned

Through this project, I learned how to:

- Create reusable Terraform modules.
- Provision Amazon EC2 instances using Terraform.
- Configure Security Groups as separate modules.
- Use User Data scripts to automate server configuration.
- Install and configure Apache automatically.
- Connect Terraform modules using outputs and variables.
- Validate, plan, apply, and destroy Terraform infrastructure.
- Verify successful deployments through a web browser.
- Apply Infrastructure as Code (IaC) best practices.

---

# Conclusion

This mini project provided practical experience with modular Infrastructure as Code using Terraform. I successfully provisioned an Amazon EC2 instance and a reusable Security Group module while using a User Data script to automate the installation and configuration of the Apache HTTP Server.

The successful display of the **"Hello World from Terraform!"** webpage confirmed that both the infrastructure and server configuration were fully automated. This project reinforced the benefits of Terraform modules, reusable infrastructure, automated server provisioning, and Infrastructure as Code for managing cloud resources efficiently.
