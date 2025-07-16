
# AWS VPC Project:Configuration Steps

## Part 1: Setting Up a Virtual Private Cloud (VPC)
1. Navigate to the AWS search bar and enter "VPC".
2. Click on the VPC result to go to the VPC dashboard.
3. Click **Create VPC**.
4. Select **VPC only**, specify the IPv4 CIDR block (e.g., `10.0.0.0/16`), and click **Create VPC**.
> ⚠️ If you encounter an error stating the CIDR block must be between `/16` and `/28`, ensure your provided block falls within that range.

---

## Part 2: Configuring Subnets within the VPC
1. On the left sidebar, click **Subnets**, then click **Create subnet**.
2. Select the VPC you just created.
3. Enter subnet name, choose availability zone, and specify IPv4 CIDR (e.g., `10.0.1.0/24` for public and `10.0.2.0/24` for private).
4. To add more subnets, click **Add subnet**, repeating the process.
5. Click **Create subnet** once all are added.
> ✅ Use meaningful names to distinguish public and private subnets.

---

## Part 3: Creating and Attaching an Internet Gateway
1. Navigate to **Internet Gateways**.
2. Click **Create Internet Gateway**, name it, and click **Create**.
3. Once created, attach it to your VPC by selecting **Actions > Attach to VPC**.

---

## Part 4: Enabling Internet Connectivity with Route Tables
1. Go to **Route Tables**, click **Create route table**.
2. Name it and associate it with the VPC.
3. Go to **Subnet Associations > Edit subnet associations** and attach the **public subnet**.
4. Go to **Routes > Edit routes > Add route**:
   - Destination: `0.0.0.0/0`
   - Target: Your **Internet Gateway**
5. Save the route.

---

## Part 5: Enabling Outbound Access via NAT Gateway
1. Navigate to **NAT Gateways**, click **Create NAT Gateway**.
2. Name it and place it in a **public subnet**.
3. Allocate or use an Elastic IP for it.
4. After creation:
   - Go to your **private subnet’s route table**
   - Add a route with:
     - Destination: `0.0.0.0/0`
     - Target: Your **NAT Gateway**
5. Associate the private subnet with this route table.

---

## Part 6: Establishing VPC Peering Connection
1. Create two VPCs (same region or cross-region as needed).
2. Navigate to **Peering Connections** > **Create Peering Connection**.
3. Specify:
   - Name
   - Requester and Accepter VPC
   - Account: My account
   - Region: This region
4. Click **Create Peering Connection**.
5. Accept the connection from **Actions > Accept Request**.
6. Update **route tables** in both VPCs:
   - Add route to the **peer VPC CIDR block**
   - Target: **Peering connection**

Example:
- VPC A CIDR: `192.168.0.0/16`
- VPC B CIDR: `172.16.0.0/16`
- Each VPC’s route table must have a route pointing to the other via the peering connection.

> ✅ After this, instances in each VPC can communicate with each other if security group rules allow it.

---

## ✅ Result
You’ve configured:
- A VPC with public and private subnets
- Internet access for public subnet
- Outbound access for private subnet using NAT
- Inter-VPC communication via peering

