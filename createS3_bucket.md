
# 📦 AWS S3 Bucket Project Guide – Instructor-Compliant Submission

## ✅ Objective

This guide demonstrates how to:
- Create an Amazon S3 bucket
- Upload a text file to the bucket
- Enable versioning with verification
- Configure bucket policy to allow public access to the object
- Implement a lifecycle rule to transition objects to a lower-cost storage class

All steps include clear instructions, screenshots (where applicable), and align with the specific tasks required by the instructor.

---

## 🔧 Prerequisites

- Active AWS account
- IAM user with full access to Amazon S3
- AWS region selected (e.g., `us-east-1`)

---

## 1️⃣ Create an S3 Bucket

1. Go to **AWS Console > S3 > Create Bucket**
2. Enter a **globally unique bucket name** (e.g., `gatogrowfast-s3-demo`)
3. Choose region (e.g., `us-east-1`)
4. Leave **Block Public Access** as checked (we will later add bucket policy)
5. Keep other settings default and click **Create Bucket**

📸 *Screenshot: Bucket successfully created*
<img width="946" alt="Image" src="https://github.com/user-attachments/assets/b60b0695-f933-4a58-b788-bfd611784740" />

---

## 2️⃣ Upload a Text File to the Bucket

1. Click on the bucket name
2. Click **Upload > Add Files**
3. Choose a `.txt` file (e.g., `welcome.txt`)
4. Click **Upload**

<img width="929" alt="Image" src="https://github.com/user-attachments/assets/dfbcaf47-2e61-442d-845b-acb83fcc0723" />
---

## 3️⃣ Enable Versioning

1. From the bucket dashboard, click **Properties**
2. Scroll to **Bucket Versioning**
3. Click **Edit**, then **Enable**, and **Save changes**

✅ **Test Versioning:**
1. Upload a new version of `welcome.txt` (overwrite with a new version)
2. Go to **Objects > Versions** to confirm multiple versions exist

📸 *Screenshot: Object versions visible*
<img width="941" alt="Image" src="https://github.com/user-attachments/assets/42eb06c8-c5ca-46ed-afbb-9a93817e4d2b" />

---
## ✅ Task 4: Implement Lifecycle Policy in Amazon S3

This section documents the configuration of a lifecycle policy for the S3 bucket `gatogrowfast-s3-demo`. The goal is to transition objects to a lower-cost storage class, demonstrating cost optimization in S3 storage management.
## 🪜 Steps to Create a Lifecycle Policy

1. Go to the **AWS S3 Console**
2. Select the bucket `gatogrowfast-s3-demo`
3. Click the **Management** tab
4. Under **Lifecycle rules**, click **Create lifecycle rule**

---

### ✏️ Rule Configuration

- **Rule name**: `TransitionToStandardIA`
- **Scope**: Apply to **all objects**
- **Rule action**:  
  ✅ Transition current versions of objects

---

### 🛠️ Transition Settings

- Transition to: **Standard-IA (Infrequent Access)**
- Days after object creation: `30`
<img width="938" alt="Image" src="https://github.com/user-attachments/assets/393f40cd-443f-45b3-a84c-36c28d0a1f07" />
<img width="946" alt="Image" src="https://github.com/user-attachments/assets/11fc9f1a-734a-4d90-95bb-59ae525ad249" />

## 5 Configure Bucket Policy for Public Access
<img width="935" alt="Image" src="https://github.com/user-attachments/assets/bb24a543-afa9-416f-911f-8780a61dd48a" />
<img width="959" alt="Image" src="https://github.com/user-attachments/assets/1c92817f-fb40-48de-a62e-cfd7ce682d39" />
1. Go to **Permissions > Bucket Policy**
2. Add the following JSON policy to allow public read access:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::gatogrowfast-s3-demo/*"
    }
  ]
}


---
