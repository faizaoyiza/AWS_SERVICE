# AWS Security Group and NACL Configuration Guide

This document provides step-by-step instructions on how to configure Security Groups and Network Access Control Lists (NACLs) in AWS to control inbound and outbound traffic for your EC2 instances.

---

## 1. Security Groups

### Overview
Security Groups act as virtual firewalls for your EC2 instances, controlling inbound and outbound traffic at the instance level.

---

### Step 1: Create a Security Group Allowing HTTP Traffic for All Traffic

**Objective:** Allow HTTP (port 80) traffic from anywhere and attach this security group to your EC2 instance.

**Steps:**
1. Open the [AWS Management Console](https://console.aws.amazon.com/ec2/).
2. Navigate to **Security Groups** under **Network & Security**.
3. Click **Create Security Group**.
4. Enter a **Name** and **Description**.
5. Select your **VPC**.
6. Under **Inbound rules**:
   - Click **Add Rule**.
   - Type: **HTTP**.
   - Protocol: **TCP**.
   - Port Range: **80**.
   - Source: **Anywhere** (0.0.0.0/0).
7. Under **Outbound rules**:
   - Leave default (Allow All Traffic) or customize as needed.
8. Click **Create**.
9. Attach this security group to your EC2 instance:
   - Go to **Instances**.
   - Select your instance.
   - Click **Actions** > **Security** > **Change Security Groups**.
   - Select the new security group and save.

---

### Scenario 2: Implement inbound traffic rules for HTTP and SSH, and allow outbound traffic for all

**Steps:**
1. Edit your security group.
2. Add inbound rules:
   - HTTP (port 80): Source 0.0.0.0/0.
   - SSH (port 22): Source (your IP or 0.0.0.0/0 for testing).
3. Ensure **Outbound rules** allow all traffic:
   - Protocol: All.
   - Port Range: All.
   - Destination: 0.0.0.0/0.
4. Save changes.

---

### Scenario 3: Configure inbound rules for HTTP with NO outbound rules

**Steps:**
1. Edit your existing security group.
2. Remove all outbound rules or set outbound rules to **Deny** (by default, security groups are stateful and allow all outbound traffic, so to restrict outbound, create a custom outbound rule that denies traffic).
3. For inbound:
   - Add HTTP (port 80): Source 0.0.0.0/0.
4. Save.

---

### Scenario 4: Remove both inbound and outbound rules

**Steps:**
1. In your security group:
   - Remove all inbound rules.
   - Remove all outbound rules.
2. Save changes.

**Note:** AWS security groups **default to allow all outbound traffic**, so to restrict outbound, you need to create custom rules.

---

### Scenario 5: No inbound rules but outbound rules for all traffic

**Steps:**
1. Remove all inbound rules.
2. Keep or create outbound rules:
   - Protocol: All.
   - Port Range: All.
   - Destination: 0.0.0.0/0.
3. Save.

---
<img width="950" height="440" alt="Image" src="https://github.com/user-attachments/assets/f822b91f-6ac8-4166-8328-a6ca1f136b09" />
<img width="944" height="433" alt="Image" src="https://github.com/user-attachments/assets/26bd5aed-606c-4cb7-974d-a5c8a80a940d" />
## 2. Network Access Control Lists (NACLs)

### Overview
NACLs are stateless firewalls at the subnet level, controlling inbound and outbound traffic for all resources within the subnet.

---

### Step 1: Examine Default NACL Settings

- **Default inbound rules**:
  - Allow all traffic from 0.0.0.0/0.
- **Default outbound rules**:
  - Allow all traffic to 0.0.0.0/0.

---

### Step 2: Modify NACL Rules

**Objective:** Change rules to permit specific traffic.

**Steps:**
1. In the AWS Console, navigate to **VPC** > **Network ACLs**.
2. Select the default NACL or create a new one.
3. Edit **Inbound Rules**:
   - Add a rule:
     - Rule number: e.g., 100.
     - Type: **All Traffic**.
     - Protocol: **All**.
     - Port Range: **All**.
     - Source: **0.0.0.0/0**.
     - Allow/Deny: **Allow**.
4. Edit **Outbound Rules**:
   - Add a rule:
     - Rule number: e.g., 100.
     - Type: **All Traffic**.
     - Protocol: **All**.
     - Port Range: **All**.
     - Destination: **0.0.0.0/0**.
     - Allow/Deny: **Allow**.
5. Save changes.

---
<img width="944" height="433" alt="Image" src="https://github.com/user-attachments/assets/7f7f03c1-3864-42a6-86b8-2365311c5b2b" />
## **Summary Table**

| Scenario | Security Group Rules | NACL Rules |
| -------- | ---------------------- | ---------- |
| Allow HTTP from anywhere | Inbound: HTTP 80 from 0.0.0.0/0 | Inbound: All traffic from 0.0.0.0/0 |
| Allow HTTP & SSH, allow outbound | Inbound: HTTP 80, SSH 22 | Outbound: All traffic to 0.0.0.0/0 |
| HTTP inbound only, no outbound | Inbound: HTTP 80 | Remove outbound rules or restrict |
| Remove all inbound & outbound | Remove all rules | Remove all rules |
| No inbound, outbound allows all | Outbound: All traffic | Remove inbound rules |

---

## Additional Tips
- Always test your rules in a controlled environment.
- Use specific IP ranges for production for security.
- Remember, security groups are stateful, so return traffic is automatically allowed.

---

Feel free to modify and expand this guide based on your specific project requirements.
