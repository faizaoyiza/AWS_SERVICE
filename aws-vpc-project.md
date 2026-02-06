# AWS Networking Project: VPC, Subnets, Internet Gateway, NAT Gateway & VPC Peering

## 📌 Project Overview

This project demonstrates how to design and configure a **secure and scalable AWS networking architecture** using core AWS networking components.

The objective is to build a Virtual Private Cloud (VPC) from scratch and progressively enable internet access, outbound connectivity, and inter-VPC communication following AWS best practices.

This project is suitable for **Cloud / DevOps portfolio submission** and reflects real-world infrastructure design.

---

## 🎯 Project Objectives

By completing this project, the following AWS networking concepts are implemented:

1. Setting up a Virtual Private Cloud (VPC)
2. Configuring public and private subnets
3. Creating and attaching an Internet Gateway
4. Enabling internet connectivity using route tables
5. Enabling outbound internet access via a NAT Gateway
6. Establishing VPC Peering connections

---

## 🛠️ Services & Tools Used

- Amazon VPC
- Subnets
- Internet Gateway (IGW)
- Route Tables
- NAT Gateway
- Elastic IP (EIP)
- VPC Peering
- AWS Management Console

---

## 📂 Suggested Project Structure

```
aws-vpc-networking-project/
├── screenshots/
└── README.md
```

---

## 1️⃣ Setting Up a Virtual Private Cloud (VPC)

### What is a VPC?

A **Virtual Private Cloud (VPC)** is a logically isolated network within AWS where you can launch AWS resources in a defined virtual network.

It allows full control over:
- IP address range
- Subnets
- Route tables
- Gateways
- Network security

---

### Steps to Create a VPC

1. Log in to the **AWS Management Console**
2. Navigate to **VPC → Your VPCs**
3. Click **Create VPC**
4. Choose **VPC only**
5. Configure the following:
   - **Name tag:** `project-vpc`
   - **IPv4 CIDR block:** `10.0.0.0/16`
   - **Tenancy:** Default
6. Click **Create VPC**

---

### Outcome

- A new VPC with a `/16` CIDR block
- Enables up to 65,536 private IP addresses
- Forms the foundation for all other networking components

📸 Screenshot:

---

<img width="1910" height="776" alt="Image" src="https://github.com/user-attachments/assets/03b76011-27c9-443b-8557-8889009a25af" />

## 2️⃣ Configuring Subnets within the VPC

### Why Subnets?

Subnets divide a VPC into smaller networks and allow separation of:
- Public-facing resources
- Private internal resources

---

### Subnet Design

| Subnet Type | CIDR Block | Availability Zone |
|------------|-----------|------------------|
| Public Subnet | 10.0.1.0/24 | us-east-1a |
| Private Subnet | 10.0.2.0/24 | us-east-1b |

---

### Steps to Create Subnets

1. Go to **VPC → Subnets**
2. Click **Create subnet**
3. Select the created VPC
4. Create:
   - Public subnet
   - Private subnet
5. Assign appropriate CIDR blocks
6. Choose different Availability Zones

📸 Screenshot:

---
<img width="958" height="430" alt="Image" src="https://github.com/user-attachments/assets/e0676244-088a-4fcf-9580-59461369f855" />

## 3️⃣ Creating and Attaching an Internet Gateway

### What is an Internet Gateway?

An **Internet Gateway (IGW)** allows communication between resources in a VPC and the public internet.

---

### Steps to Create and Attach IGW

1. Navigate to **VPC → Internet Gateways**
2. Click **Create internet gateway**
3. Name it `project-igw`
4. Attach the IGW to `project-vpc`

📸 Screenshot:

---
<img width="953" height="389" alt="Image" src="https://github.com/user-attachments/assets/d65e65f3-2ef3-44a4-bd96-aaafa19f8606" />
## 4️⃣ Enabling Internet Connectivity Using Route Tables

### Why Route Tables?

Route tables determine where network traffic is directed.

---

### Steps to Configure Public Route Table

1. Go to **VPC → Route Tables**
2. Create a new route table
3. Associate it with the **public subnet**
4. Add route:
   - Destination: `0.0.0.0/0`
   - Target: Internet Gateway

This enables inbound and outbound internet access for public subnet resources.

📸 Screenshot:

---
<img width="956" height="334" alt="Image" src="https://github.com/user-attachments/assets/dbb380f0-616e-4e83-a792-e0198c89fd86" />
## 5️⃣ Enabling Outbound Internet Access via NAT Gateway

### What is a NAT Gateway?

A **NAT Gateway** allows instances in private subnets to access the internet **without being directly exposed**.

---

### Steps to Create NAT Gateway

1. Allocate an **Elastic IP**
2. Navigate to **VPC → NAT Gateways**
3. Create NAT Gateway in the **public subnet**
4. Attach the Elastic IP

---
<img width="956" height="334" alt="Image" src="https://github.com/user-attachments/assets/dd4dff73-516c-4306-a08d-caeaeaa9e448" />
### Configure Private Route Table

1. Create a new route table
2. Associate it with the **private subnet**
3. Add route:
   - Destination: `0.0.0.0/0`
   - Target: NAT Gateway



## 6️⃣ Establishing VPC Peering Connections

### What is VPC Peering?

VPC Peering enables **private communication between two VPCs** using AWS’s internal network.

---

### Steps to Create VPC Peering

1. Go to **VPC → Peering Connections**
2. Create peering connection
3. Select:
   - Requester VPC
   - Accepter VPC
4. Accept the peering request

---

### Update Route Tables

Add routes in both VPCs:
- Destination: Peer VPC CIDR
- Target: VPC Peering Connection

📸 Screenshot:

---
<img width="950" height="410" alt="Image" src="https://github.com/user-attachments/assets/ee307cd3-ad1a-45f9-aa6e-9e4f9197a134" />

## 🚀 Key Takeaways

- Designed a secure AWS VPC architecture
- Implemented public and private subnet separation
- Enabled controlled internet access
- Applied AWS networking best practices
- Built a scalable foundation for cloud workloads

---

## ✅ Conclusion

This project demonstrates hands-on experience with **AWS VPC networking fundamentals**, which are essential for cloud engineering, DevOps roles, and real-world infrastructure deployment.

The architecture created here can support applications such as:
- Web applications
- CI/CD pipelines
- Microservices architectures
