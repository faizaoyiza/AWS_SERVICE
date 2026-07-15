# High Availability WordPress Website on AWS

## Introduction

This project demonstrates how to deploy a **Highly Available WordPress Website on AWS** using the following AWS services:

- Amazon EC2
- Amazon RDS (MySQL)
- Amazon EFS (Elastic File System)
- Application Load Balancer (ALB)
- Auto Scaling Group (ASG)

This architecture ensures high availability, fault tolerance, scalability, and centralized storage for WordPress.

---

# Prerequisites

Before beginning, ensure you have:

- An active AWS account
- Basic knowledge of AWS services
- Access to the AWS Management Console
- SSH client for connecting to EC2 instances
- Basic Linux command-line knowledge

---

# Architecture Overview

The solution consists of the following AWS services:

- **Amazon EC2** – Hosts the WordPress application.
- **Amazon RDS (MySQL)** – Stores the WordPress database.
- **Amazon EFS** – Provides shared storage for WordPress media uploads.
- **Application Load Balancer (ALB)** – Distributes incoming traffic across EC2 instances.
- **Auto Scaling Group (ASG)** – Automatically scales EC2 instances based on demand.

This architecture ensures that WordPress remains available even if an EC2 instance fails.

---

# Solution Architecture

```text
                    Internet
                        │
                        ▼
          ┌────────────────────────┐
          │ Application Load       │
          │ Balancer (ALB)         │
          └──────────┬─────────────┘
                     │
        ┌────────────┴────────────┐
        ▼                         ▼
 ┌──────────────┐          ┌──────────────┐
 │ EC2 Instance │          │ EC2 Instance │
 │ WordPress    │          │ WordPress    │
 └──────┬───────┘          └──────┬───────┘
        │                         │
        └────────────┬────────────┘
                     │
                     ▼
             Amazon EFS
      (Shared WordPress Files)

                     │
                     ▼

             Amazon RDS MySQL
        (Shared WordPress Database)
```

---

# Step 1 — Launch an EC2 Instance

Navigate to:

**AWS Console → EC2 → Launch Instance**

Configure the instance as follows:

- **AMI:** Amazon Linux 2 (or preferred Linux distribution)
- **Instance Type:** `t2.micro` (Free Tier)
- **Storage:** Minimum 8 GB
- **Security Group:**
  - SSH (22)
  - HTTP (80)
  - HTTPS (443)

---

## Install the LAMP Stack

Connect to the EC2 instance via SSH and run:

```bash
sudo yum update -y

sudo yum install -y httpd mariadb-server php php-mysqlnd php-fpm

sudo systemctl start httpd
sudo systemctl enable httpd

sudo systemctl start mariadb
sudo systemctl enable mariadb
```

---

## Install WordPress

```bash
cd /var/www/html

wget https://wordpress.org/latest.tar.gz

tar -xvzf latest.tar.gz

sudo mv wordpress/* /var/www/html/

sudo chown -R apache:apache /var/www/html/
```

---

# Step 2 — Create an Amazon RDS MySQL Database

Navigate to:

**AWS Console → RDS → Create Database**

Configure the database:

- Engine: **MySQL**
- DB Instance Class: `db.t3.micro`
- DB Identifier:

```text
wordpress-db
```

Master Username:

```text
admin
```

Create a secure password.

---

## Configure Security Group

Allow inbound MySQL traffic:

| Type | Port | Source |
|------|------|--------|
| MySQL | 3306 | EC2 Security Group |

---

## Record the RDS Endpoint

Example:

```text
wordpress-db.c2vm8m2euuke.us-east-1.rds.amazonaws.com
```

---

# Step 3 — Create Amazon EFS

Navigate to:

**AWS Console → EFS → Create File System**

Configure:

- VPC
- Availability Zones
- Mount Targets
- Security Group

Create the file system.

---

## Mount EFS on EC2

Install EFS utilities:

```bash
sudo yum install -y amazon-efs-utils
```

Create the mount directory:

```bash
sudo mkdir -p /mnt/efs
```

Mount the file system:

```bash
sudo mount -t efs fs-xxxx:/ /mnt/efs
```

Replace:

```text
fs-xxxx
```

with your EFS File System ID.

---

## Configure WordPress Upload Directory

Create the uploads directory:

```bash
sudo mkdir -p /mnt/efs/wp-content/uploads
```

Create a symbolic link:

```bash
sudo ln -s /mnt/efs/wp-content/uploads /var/www/html/wp-content/uploads
```

Set ownership:

```bash
sudo chown -R apache:apache /mnt/efs/wp-content/uploads
```

WordPress media files will now be stored on Amazon EFS.

---

# Step 4 — Configure WordPress

Edit the WordPress configuration file:

```bash
sudo nano /var/www/html/wp-config.php
```

Update the database configuration:

```php
define('DB_NAME', 'wordpress-db');
define('DB_USER', 'admin');
define('DB_PASSWORD', 'yourpassword');
define('DB_HOST', 'wordpress-db.c2vm8m2euuke.us-east-1.rds.amazonaws.com');
```

Replace:

- Database name
- Username
- Password
- RDS endpoint

with your actual values.

Save the file.

WordPress will now use:

- Amazon RDS for database storage
- Amazon EFS for media uploads

---

# Step 5 — Configure an Application Load Balancer

Navigate to:

**AWS Console → EC2 → Load Balancers**

Create an **Application Load Balancer**.

Configuration:

- Internet-facing
- HTTP (80)
- HTTPS (443) *(optional but recommended)*
- Select your VPC
- Select public subnets

---

## Create a Target Group

Create a Target Group for your EC2 instances.

Register the WordPress EC2 instance.

Attach the Target Group to the ALB.

---

## Update Security Groups

Allow:

- HTTP (80)
- HTTPS (443)

from the ALB to the EC2 instances.

---

# Step 6 — Configure an Auto Scaling Group

Navigate to:

**AWS Console → EC2 → Auto Scaling Groups**

Create a Launch Template using the WordPress EC2 instance.

Configure the Auto Scaling Group.

Example settings:

| Setting | Value |
|----------|-------|
| Desired Capacity | 1 |
| Minimum | 1 |
| Maximum | 3 |

Attach the Application Load Balancer.

---

## Configure Scaling Policy

Example policy:

Scale Out:

- CPU Utilization > 70%

Scale In:

- CPU Utilization < 30%

This allows AWS to automatically increase or decrease the number of WordPress servers.

---

# Testing the Deployment

## 1. Access WordPress

Open the ALB DNS name:

```text
http://my-load-balancer-12345.elb.amazonaws.com
```

Complete the WordPress installation.

---

## 2. Verify EFS

Upload:

- Images
- Themes
- Plugins

Check the uploads directory:

```bash
ls /mnt/efs/wp-content/uploads
```

The uploaded files should appear.

---

## 3. Verify RDS

Create posts or pages.

Refresh the website.

Data should remain available because it is stored in Amazon RDS.

---

## 4. Test Auto Scaling

Generate traffic or manually increase desired capacity.

Verify:

- Additional EC2 instances launch.
- They automatically join the Load Balancer.
- Traffic is distributed across all instances.

---

# Troubleshooting

## EC2 Instance Not Accessible

Check:

- Security Group rules
- Route Tables
- Internet Gateway
- Load Balancer Target Health

---

## Database Connection Errors

Verify:

- RDS endpoint
- Username
- Password
- Security Group
- Port 3306 access

---

## EFS Mount Fails

Check:

- EFS Mount Targets
- Security Group (NFS Port 2049)
- VPC configuration
- File System ID

---

## WordPress Media Not Shared

Ensure:

- EFS is mounted correctly
- Symbolic link exists
- Directory permissions are correct

---

# Best Practices

- Use private subnets for EC2 and RDS.
- Store database credentials in **AWS Secrets Manager** or **SSM Parameter Store**.
- Enable HTTPS using **AWS Certificate Manager (ACM)**.
- Enable automatic backups for Amazon RDS.
- Configure EFS Lifecycle Management to reduce storage costs.
- Enable CloudWatch monitoring and alarms.
- Restrict security group access using the principle of least privilege.

---

# Key Learnings

- Deploy WordPress on Amazon EC2.
- Store the WordPress database in Amazon RDS.
- Share WordPress media files using Amazon EFS.
- Distribute traffic with an Application Load Balancer.
- Automatically scale EC2 instances using an Auto Scaling Group.
- Build a highly available, scalable, and fault-tolerant WordPress architecture on AWS.

---

# Conclusion

In this project, we successfully deployed a **Highly Available WordPress Website on AWS** using Amazon EC2, Amazon RDS, Amazon EFS, an Application Load Balancer, and an Auto Scaling Group. This architecture provides centralized database storage, shared file storage, load balancing, and automatic scaling, ensuring high availability, improved performance, and resilience against failures. The solution also follows AWS best practices for scalability, fault tolerance, and production-ready WordPress deployments.

---

**End of Project**
