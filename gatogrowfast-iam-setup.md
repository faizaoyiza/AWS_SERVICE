
# AWS IAM Setup for GatoGrowFast.com Employees

GatoGrowFast.com, a growth marketing consultancy, wants to provide AWS resource access to their employees: Eric, Jack, and Ade. This setup will be done in two parts.

---

## Part 1: Create IAM Policy and User for Eric

### Step 1: Create a Policy with Full EC2 Access
1. Sign in to the **AWS Management Console**.
2. Navigate to **IAM > Policies**.
3. Click **Create Policy**.
4. In the **Service** section, select **EC2**.
5. Under **Actions**, choose **All EC2 Actions**.
6. Under **Resources**, select **All Resources**.
7. Click **Next**, then give the policy a name, e.g., `FullEC2AccessPolicy`.
8. Provide a description (e.g., "Provides full access to EC2 resources") and click **Create Policy**.

### Step 2: Create a User for Eric and Attach Policy
1. Go to **IAM > Users**.
2. Click **Add Users**.
3. Enter the username `Eric`.
4. Select **Access type**: Programmatic access and AWS Management Console access.
5. Click **Next: Permissions**.
6. Choose **Attach existing policies directly**.
7. Search for `FullEC2AccessPolicy` and select it.
8. Click **Next** and then **Create User**.
9. Download Eric’s credentials or send login instructions.

---
<img width="935" alt="Image" src="https://github.com/user-attachments/assets/df082870-3428-4677-9628-7b135486a893" />
## Part 2: Setup Group and Users Jack and Ade

### Step 1: Create a User Group
1. Go to **IAM > User Groups**.
2. Click **Create Group**.
3. Enter group name: `MarketingTechTeam`.
4. Do not attach policies yet—this will be done after policy creation.

### Step 2: Create Users Jack and Ade
1. Go to **IAM > Users > Add Users**.
2. Add `Jack` and `Ade` as users with AWS Management Console access.
3. Add them to the group `MarketingTechTeam`.
<img width="959" alt="Image" src="https://github.com/user-attachments/assets/3a1b86fd-9202-4023-b9e1-1eabd28fcc65" />

### Step 3: Create a Policy with Full Access to EC2 and S3
1. Go to **IAM > Policies > Create Policy**.
2. Select **EC2** and **S3** services.
3. For each service, choose **All Actions** and **All Resources**.
4. Click **Next**, then name the policy `FullEC2S3AccessPolicy`.
5. Provide a description (e.g., "Grants full access to EC2 and S3") and click **Create Policy**.

### Step 4: Attach Policy to Group
1. Navigate to **IAM > User Groups > MarketingTechTeam**.
2. Click **Add Permissions**.
3. Select **Attach Policies**, search for `FullEC2S3AccessPolicy`, and select it.
4. Click **Add Permissions**.

---
<img width="953" alt="Image" src="https://github.com/user-attachments/assets/92c4b429-ef79-4138-bdbe-9048f94d9341" />
## Validation

### Eric
- Should be able to access **only EC2** resources.

### Jack and Ade
- Should be able to access **both EC2 and S3** services.

This setup adheres to the **principle of least privilege**, giving users only the access they need.

---
<img width="943" alt="Image" src="https://github.com/user-attachments/assets/77b185be-7515-4769-83f1-b00924c72e1d" />

