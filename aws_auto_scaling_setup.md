 🚀 AWS EC2 Auto Scaling Setup Guide

## ✅ Task 1: Create Launch Template
1. **Log in** to the AWS Management Console.
2. Navigate to **EC2** from the Services menu.
3. In the left navigation pane, click **Launch Templates**.
4. Click **Create launch template**.
5. Fill in the following:
   - **Launch template name**: (e.g., `my-launch-template`)
   - **AMI ID**: Select a valid Amazon Machine Image (e.g., Amazon Linux 2)
   - **Instance type**: (e.g., `t2.micro`)
   - **Key pair**: Choose an existing key pair for SSH access
   - **Network settings**: Use default VPC/subnet or customize
   - **User data** (optional): Add bootstrapping script (Base64-encoded)
   - Example:
     ```bash
     #!/bin/bash
     yum update -y
     yum install httpd -y
     systemctl start httpd
     systemctl enable httpd
     echo "Hello from Launch Template" > /var/www/html/index.html
     ```

6. Click **Create launch template** to save.

## ✅ Task 2: Set Up Auto Scaling Group
1. Go to **EC2 > Auto Scaling Groups**.
2. Click **Create Auto Scaling group**.
3. Enter a name (e.g., `my-asg`), and select:
   - **Launch Template** > Choose the one created earlier.
4. Click **Next** to configure:
   - **VPC and Subnets**: Select existing ones where EC2 can launch.
   - **Load Balancing**: You can skip for now; will be done in Task 4.
   - **Group Size**:
     - Desired: `1`
     - Minimum: `1`
     - Maximum: `3`
5. Click **Next** and **Create Auto Scaling Group**.

## ✅ Task 3: Configure Scaling Policies
1. After creating the group, select your **Auto Scaling group**.
2. Go to the **Automatic scaling** tab.
3. Click **Create a policy** under **Scaling Policies**.
4. Choose **Target tracking scaling policy**.
   - Example:
     - **Policy name**: `cpu-scaling`
     - **Metric type**: `Average CPU Utilization`
     - **Target value**: `50%`
     - **Instances between**: Min `1`, Max `3`
5. Click **Create** to apply the policy.

## ✅ Task 4: Attach ALB to Auto Scaling Group
1. In the **Auto Scaling Group**, click **Edit**.
2. Scroll to **Load balancing**.
3. Choose **Attach to an existing load balancer**:
   - Select an **Application Load Balancer (ALB)**.
   - Choose the appropriate **target group** for instance registration.
4. Save changes.

## ✅ Task 5: Test Auto Scaling
1. **SSH into your EC2 instance** from the Auto Scaling Group.
2. **Install stress tool**:
   ```bash
   sudo amazon-linux-extras install epel -y
   sudo yum install stress -y
   ```
3. **Generate CPU load** to trigger scaling:
   ```bash
   stress --cpu 2 --timeout 300
   ```
4. In the EC2 Console:
   - Go to **Auto Scaling Groups**.
   - Monitor the **Instance count** as it scales out/in based on demand.
5. To simulate scale in, stop the stress tool and observe the scaling-in behavior.
