# AWS VPC, Security Groups, and NACL Configuration Guide

This document provides a complete, step-by-step guide to setting up a Virtual Private Cloud (VPC), subnets, Internet Gateway, Security Groups, and Network Access Control Lists (NACLs) in AWS. It also covers testing and troubleshooting your network configurations.

---

## Table of Contents
1. Creating a VPC with Public and Private Subnets
2. Attaching an Internet Gateway
3. Configuring Route Tables
4. Creating and Attaching Security Groups
5. Configuring NACLs with Rule Precedence
6. Testing Network Rules
7. Troubleshooting and Best Practices

---

## 1. Creating a VPC with Public and Private Subnets

### Step 1: Create a VPC
1. Open the [VPC Dashboard](https://console.aws.amazon.com/vpc/).
2. Click **Create VPC**.
3. Choose **VPC only**.
4. Enter a Name (e.g., `MyVPC`).
5. IPv4 CIDR block: `10.0.0.0/16`.
6. Click **Create**.

### Step 2: Create Public and Private Subnets
1. In your VPC dashboard, select your VPC.
2. Go to **Subnets** > **Create subnet**.
3. Create a **Public Subnet**:
   - Name: `PublicSubnet`.
   - Availability Zone: choose one.
   - CIDR block: `10.0.1.0/24`.
4. Create a **Private Subnet**:
   - Name: `PrivateSubnet`.
   - CIDR block: `10.0.2.0/24`.

---

## 2. Attaching an Internet Gateway
1. In the VPC dashboard, select **Internet Gateways** > **Create internet gateway**.
2. Name it `MyIGW`.
3. Attach the IGW to your VPC:
   - Select the IGW.
   - Click **Actions** > **Attach to VPC** > select your VPC.

---

## 3. Configuring Route Tables
### Public Route Table:
1. Create a route table named `PublicRT`.
2. Associate it with your **Public Subnet**.
3. Edit routes:
   - Add route: Destination `0.0.0.0/0`, Target: your **Internet Gateway** (`MyIGW`).

### Private Route Table:
1. Create a route table named `PrivateRT`.
2. Associate it with your **Private Subnet**.
3. No need for an internet route unless NAT Gateway is used.

---

## 4. Creating and Attaching Security Groups

### Scenario 1: Basic HTTP Traffic Allowance
1. Navigate to **Security Groups** > **Create Security Group**.
2. Name: `AllowHTTP`.
3. VPC: select your VPC.
4. Inbound rules:
   - Type: HTTP, Protocol: TCP, Port: 80, Source: 0.0.0.0/0.
5. Outbound rules:
   - Allow all (default).
6. Attach this security group to your EC2 instance.

### Scenario 2: Inbound HTTP and SSH, Outbound All Traffic
- Add inbound rules:
  - HTTP (port 80): 0.0.0.0/0.
  - SSH (port 22): your IP or 0.0.0.0/0.
- Outbound:
  - Allow all.

### Scenario 3: HTTP Inbound Only, No Outbound Rules
- Remove all outbound rules.
- Keep inbound rules for HTTP only.

### Scenario 4: Remove All Rules
- Delete all inbound and outbound rules in the security group.

### Scenario 5: No Inbound, Outbound All Traffic
- Remove inbound rules.
- Keep outbound rules allowing all traffic.

---
<img width="950" height="440" alt="Image" src="https://github.com/user-attachments/assets/f822b91f-6ac8-4166-8328-a6ca1f136b09" />
<img width="944" height="433" alt="Image" src="https://github.com/user-attachments/assets/26bd5aed-606c-4cb7-974d-a5c8a80a940d" />
## 5. Configuring NACLs with Rule Precedence

### Step 1: Examine Default NACL
- Default inbound: allows all traffic from 0.0.0.0/0.
- Default outbound: allows all traffic to 0.0.0.0/0.

### Step 2: Create or Modify a Custom NACL
1. Navigate to **VPC** > **Network ACLs**.
2. Select default or create a new NACL.
3. **Inbound Rules**:
   - Add rule:
     - Rule number: 100.
     - Type: All Traffic.
     - Protocol: All.
     - Port Range: All.
     - Source: 0.0.0.0/0.
     - Allow.
4. **Outbound Rules**:
   - Add rule:
     - Rule number: 100.
     - Type: All Traffic.
     - Protocol: All.
     - Port Range: All.
     - Destination: 0.0.0.0/0.
     - Allow.

### Note on Rule Precedence:
- Rules are evaluated in order of **rule number** (lowest to highest).
- The first matching rule applies. Be sure to assign rule numbers carefully for priority.

---

<img width="944" height="433" alt="Image" src="https://github.com/user-attachments/assets/7f7f03c1-3864-42a6-86b8-2365311c5b2b" />
## 6. Testing Network Rules

### Tools:
- **SSH**: Test SSH access to your EC2 instance.
- **Curl** or browser: Test HTTP access (`http://<public-ip>`).
- **Ping**: Test ICMP traffic if allowed.

### Testing Steps:
1. **SSH**:
   ```bash
   ssh -i your-key.pem ec2-user@<public-ip>

<img width="680" height="15" alt="Image" src="https://github.com/user-attachments/assets/931e9791-ff4b-4518-b21f-fc5a3c9a37eb" />
