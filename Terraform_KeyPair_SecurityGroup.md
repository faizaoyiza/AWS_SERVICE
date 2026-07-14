# Mini Project: Provisioning an AWS EC2 Instance with Terraform, Key Pair, Security Group, and User Data

## Introduction

In this mini project, I used **Terraform** to automate the provisioning of an Amazon EC2 instance on AWS. Rather than creating cloud resources manually through the AWS Management Console, Terraform allowed me to define the infrastructure as code (IaC), making the deployment repeatable, consistent, and easier to manage.

As part of the project, I generated a new SSH key pair, created a security group that allows HTTP and SSH access, and used a **User Data** script to automatically install and configure the Apache HTTP Server during instance launch. Finally, I verified the deployment by accessing the web server through the EC2 instance's public IP address.

---

# Project Objectives

The objectives of this project were to:

- Automate EC2 instance creation using Terraform.
- Generate an SSH key pair for secure access.
- Configure a security group to allow HTTP and SSH traffic.
- Execute a User Data script during instance launch.
- Automatically install and configure the Apache HTTP Server.
- Verify that the web server is accessible through a browser.
- Destroy the infrastructure when it is no longer needed.

---

# Prerequisites

Before starting the project, the following tools were installed and configured:

- Terraform
- AWS CLI
- AWS Account
- Visual Studio Code
- Git (optional)

I also configured AWS credentials using:

```bash
aws configure
```

This command prompted for:

- AWS Access Key ID
- AWS Secret Access Key
- Default Region
- Output Format

---

# Project Directory

I created a new project directory.

```bash
mkdir terraform-ec2-keypair
```

Then navigated into it.

```bash
cd terraform-ec2-keypair
```

Next, I created the Terraform configuration file.

```bash
touch main.tf
```

---

# Terraform Configuration

The `main.tf` file contains all the infrastructure definitions required for this project.

The configuration includes:

- AWS Provider
- SSH Key Pair
- Security Group
- EC2 Instance
- User Data Script
- Outputs

---

# Configuring the AWS Provider

Terraform first connects to AWS using the configured provider.

Example:

```hcl
provider "aws" {
  region = "us-east-1"
}
```

This specifies the AWS region where the infrastructure will be deployed.

---

# Creating an SSH Key Pair

To securely connect to the EC2 instance, Terraform generates a new key pair.

Example:

```hcl
resource "tls_private_key" "generated" {
  algorithm = "RSA"
  rsa_bits  = 4096
}

resource "aws_key_pair" "generated_key" {
  key_name   = "terraform-key"
  public_key = tls_private_key.generated.public_key_openssh
}
```

The private key can then be saved locally for SSH access.

This removes the need to manually create key pairs in AWS.

---

# Creating the Security Group

To allow access to the web server and SSH, I created a custom security group.

Example:

```hcl
resource "aws_security_group" "web_sg" {

  name = "terraform-web-sg"

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

- SSH access on Port **22**
- HTTP access on Port **80**

---

# Launching the EC2 Instance

Terraform provisions an EC2 instance using the generated key pair and security group.

Example:

```hcl
resource "aws_instance" "web_server" {

  ami = "ami-xxxxxxxx"

  instance_type = "t2.micro"

  key_name = aws_key_pair.generated_key.key_name

  vpc_security_group_ids = [
    aws_security_group.web_sg.id
  ]

  user_data = file("userdata.sh")

}
```

The EC2 instance automatically executes the User Data script during launch.

---

# Configuring User Data

One of the main requirements of this project was to configure the EC2 instance automatically using User Data.

I created a file named:

```text
userdata.sh
```

The script installs and starts Apache automatically.

Example:

```bash
#!/bin/bash

yum update -y

yum install httpd -y

systemctl start httpd

systemctl enable httpd

echo "<h1>Hello World from Terraform!</h1>" > /var/www/html/index.html
```

This script performs the following actions:

- Updates installed packages.
- Installs Apache HTTP Server.
- Starts the Apache service.
- Enables Apache to start automatically after reboot.
- Creates a simple web page displaying **Hello World from Terraform!**

Because the script executes automatically during instance creation, no manual configuration is required.

---

# Initializing Terraform

After completing the configuration, I initialized the project.

```bash
terraform init
```

Terraform downloaded the required provider plugins and prepared the working directory.

---

# Validating the Configuration

Before provisioning resources, I validated the Terraform configuration.

```bash
terraform validate
```

Terraform confirmed that the configuration syntax was correct.

---

# Reviewing the Execution Plan

Next, I generated an execution plan.

```bash
terraform plan
```

This displayed all the AWS resources Terraform intended to create before making any changes.

---

# Provisioning the Infrastructure

To create the infrastructure, I executed:

```bash
terraform apply
```

After confirmation, Terraform created:

- SSH Key Pair
- Security Group
- EC2 Instance

The User Data script automatically installed Apache during the provisioning process.

---

# Verifying Apache Installation

After the EC2 instance entered the **Running** state, I copied its public IP address from the AWS Console.

Example:

```text
http://<EC2-Public-IP>
```

When accessed through a web browser, the following page appeared:

```text
Hello World from Terraform!
```

This confirmed that:

- The User Data script executed successfully.
- Apache was installed automatically.
- The security group correctly allowed HTTP traffic.
- The web server was functioning as expected.

---

# Observations

During the project, I observed that:

- Terraform significantly reduces manual cloud provisioning tasks.
- Infrastructure can be recreated consistently using the same configuration files.
- User Data automates server configuration immediately after launch.
- Security Groups provide controlled access to cloud resources.
- Apache installation completed automatically without requiring SSH login.
- The generated key pair allowed secure remote access to the EC2 instance.

---

# Challenges Encountered

Some challenges encountered included:

- Selecting the correct Amazon Machine Image (AMI) for the AWS region.
- Ensuring the security group allowed inbound HTTP traffic.
- Waiting several minutes for the User Data script to finish executing.
- Confirming that Apache had started successfully before accessing the webpage.

These issues were resolved by reviewing Terraform output, checking the AWS EC2 console, and verifying the instance status.

---

# Destroying the Infrastructure

Once testing was complete, I removed all provisioned resources using:

```bash
terraform destroy
```

Terraform deleted:

- EC2 Instance
- Security Group
- Key Pair

This prevented unnecessary AWS charges and demonstrated the advantage of Infrastructure as Code by allowing resources to be created and removed on demand.

---

# Key Concepts Learned

Through this project, I learned how to:

- Provision AWS infrastructure using Terraform.
- Configure the AWS Provider.
- Generate SSH key pairs automatically.
- Create Security Groups using Terraform.
- Configure EC2 instances using User Data scripts.
- Automatically install Apache HTTP Server.
- Validate and plan Terraform deployments.
- Provision infrastructure using `terraform apply`.
- Remove infrastructure safely using `terraform destroy`.
- Verify successful deployment by accessing the Apache web server.

---

# Conclusion

This mini project provided practical experience with Infrastructure as Code (IaC) using Terraform. I successfully automated the deployment of an EC2 instance, generated a secure SSH key pair, configured a security group to allow SSH and HTTP traffic, and used a User Data script to automatically install and configure the Apache HTTP Server.

The successful display of the **"Hello World from Terraform!"** webpage confirmed that the infrastructure and server configuration were completed automatically. This project demonstrated how Terraform simplifies cloud infrastructure management by making deployments consistent, repeatable, and easy to maintain, while reducing manual configuration and improving operational efficiency.
