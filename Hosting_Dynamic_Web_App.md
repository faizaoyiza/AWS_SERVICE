# Mini Project: Hosting a Dynamic Web Application on AWS Using Terraform Modules, Docker, Amazon ECR, and Amazon ECS

## Introduction

In this mini project, I built and deployed a containerized dynamic web application on **Amazon Elastic Container Service (ECS)** using **AWS Fargate**. The infrastructure was provisioned using **Terraform Modules**, while **Docker** was used to containerize the application and **Amazon Elastic Container Registry (ECR)** was used to store the Docker image.

The project demonstrates a complete Infrastructure as Code (IaC) workflow—from developing a web application, containerizing it, provisioning cloud infrastructure with Terraform, storing the container image in Amazon ECR, and deploying the application to Amazon ECS.

---

# Project Objectives

The objectives of this project were to:

- Create reusable Terraform modules.
- Develop and Dockerize a dynamic Node.js web application.
- Create an Amazon ECR repository using Terraform.
- Deploy a containerized application on Amazon ECS Fargate.
- Push Docker images to Amazon ECR.
- Provision cloud infrastructure using Terraform.
- Verify that the deployed application is accessible through its public endpoint.

---

# Prerequisites

Before beginning the project, I installed and configured the following:

- AWS Account
- AWS CLI
- Docker Desktop
- Terraform
- Node.js and npm
- Visual Studio Code

I verified the installations using:

```bash
aws --version
```

```bash
docker --version
```

```bash
terraform --version
```

AWS credentials were configured using:

```bash
aws configure
```

I entered:

- AWS Access Key ID
- AWS Secret Access Key
- Default Region (e.g., us-east-1)
- Output Format

---

# Project Structure

I organized the project using Terraform modules.

```text
terraform-ecs-webapp/
│
├── app/
│   ├── Dockerfile
│   ├── index.js
│   ├── package.json
│
├── providers.tf
├── variables.tf
├── main.tf
├── outputs.tf
│
└── modules/
    ├── ecr/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf
    │
    └── ecs/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

Separating the infrastructure into modules makes the project easier to maintain and reuse.

---

# Task 1: Creating and Dockerizing the Web Application

## Creating the Node.js Application

Inside the `app` directory, I initialized a new Node.js project.

```bash
cd app
```

```bash
npm init -y
```

Next, I installed Express.

```bash
npm install express
```

I created an `index.js` file containing a simple Express application that serves a web page.

Example:

```javascript
const express = require("express");

const app = express();

app.get("/", (req, res) => {
    res.send("<h1>Welcome to my ECS Web Application!</h1>");
});

app.listen(3000);
```

I also updated the `package.json` file to include the application start script.

---

# Creating the Dockerfile

Inside the `app` directory, I created a Dockerfile.

Example:

```dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

The Dockerfile performs the following tasks:

- Downloads the Node.js base image.
- Sets the working directory.
- Copies application files.
- Installs dependencies.
- Exposes port 3000.
- Starts the application.

---

# Testing the Docker Container Locally

I built the Docker image.

```bash
docker build -t my-webapp .
```

Next, I started the container.

```bash
docker run -p 3000:3000 my-webapp
```

I verified that the application was working by visiting:

```text
http://localhost:3000
```

The application loaded successfully, confirming that the Docker image was functioning correctly.

---

# Task 2: Creating the Amazon ECR Module

The Amazon ECR module was created inside:

```text
modules/ecr/
```

The module contains:

- `variables.tf`
- `main.tf`
- `outputs.tf`

## Variables

Example:

```hcl
variable "repository_name" {
  type = string
}
```

---

## Resource

The module provisions an Amazon Elastic Container Registry repository.

Example:

```hcl
resource "aws_ecr_repository" "repository" {

  name = var.repository_name

}
```

---

## Outputs

The module exports the repository URL.

Example:

```hcl
output "repository_url" {

  value = aws_ecr_repository.repository.repository_url

}
```

This URL is later used when pushing Docker images.

---

# Task 3: Creating the Amazon ECS Module

The ECS module was created inside:

```text
modules/ecs/
```

The module provisions:

- ECS Cluster
- Task Definition
- ECS Service
- Fargate deployment

Variables include:

- Cluster Name
- Task Family
- Container Image
- CPU
- Memory

The task definition references the Docker image stored in Amazon ECR.

---

# Task 4: Root Terraform Configuration

The root `main.tf` file connects both modules.

Example:

```hcl
module "ecr" {

  source = "./modules/ecr"

  repository_name = "ecs-webapp-repo"

}

module "ecs" {

  source = "./modules/ecs"

  image = module.ecr.repository_url

}
```

Terraform automatically provisions the ECR repository before creating the ECS service.

---

# Task 5: Building and Pushing the Docker Image

## Retrieve the AWS Account ID

I obtained my AWS Account ID.

```bash
aws sts get-caller-identity --query Account --output text
```

---

## Logging into Amazon ECR

I authenticated Docker with Amazon ECR.

```bash
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com
```

---

## Building the Docker Image

```bash
docker build -t ecs-webapp .
```

---

## Tagging the Image

```bash
docker tag ecs-webapp:latest <ECR_REPOSITORY_URL>:latest
```

---

## Pushing the Image

```bash
docker push <ECR_REPOSITORY_URL>:latest
```

The Docker image was successfully uploaded to Amazon ECR.

---

# Resolving an ECR Repository Error

During deployment, I encountered the following error:

```text
The repository with name 'ecs-webapp-repo' does not exist.
```

This occurred because the repository had not yet been created.

To verify existing repositories, I executed:

```bash
aws ecr describe-repositories --region us-east-1
```

Since no repository existed, I created one manually.

```bash
aws ecr create-repository \
--repository-name ecs-webapp-repo \
--region us-east-1
```

After creating the repository, I tagged and pushed the Docker image again.

```bash
docker tag my-webapp:latest <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/ecs-webapp-repo:latest
```

```bash
docker push <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/ecs-webapp-repo:latest
```

The image was successfully stored in Amazon ECR.

---

# Task 6: Deploying the Infrastructure

I initialized Terraform.

```bash
terraform init
```

Next, I validated the configuration.

```bash
terraform validate
```

I reviewed the deployment plan.

```bash
terraform plan
```

Finally, I provisioned the infrastructure.

```bash
terraform apply
```

Terraform successfully created:

- Amazon ECR Repository
- Amazon ECS Cluster
- ECS Task Definition
- ECS Service
- AWS Fargate Resources

---

# Task 7: Accessing the Application

After deployment, I accessed the application using the public DNS or IP address associated with the ECS service.

The application loaded successfully and displayed the expected web page.

This confirmed that:

- The Docker container was running.
- Amazon ECS successfully deployed the application.
- The container image was correctly pulled from Amazon ECR.
- Terraform successfully provisioned the required AWS resources.

---

# Resources Created

The deployment created the following AWS resources:

- Amazon ECR Repository
- Amazon ECS Cluster
- ECS Task Definition
- ECS Service
- AWS Fargate Tasks
- CloudWatch Logs (for monitoring)

---

# Troubleshooting

## Docker Push Failed

Possible causes:

- Not authenticated to Amazon ECR.
- Repository does not exist.
- Insufficient IAM permissions.

**Solution**

- Authenticate using `aws ecr get-login-password`.
- Verify the repository exists.
- Ensure the IAM user has ECR permissions.

---

## ECS Task Stops Immediately

Possible causes:

- Incorrect container image.
- Port mapping errors.
- Application crashes during startup.

**Solution**

Review the ECS task logs in Amazon CloudWatch.

---

## Image Cannot Be Pulled

Possible causes:

- Incorrect repository URL.
- Image not pushed.
- Missing image tag.

**Solution**

Verify the repository URL and ensure the Docker image has been successfully uploaded.

---

# Cleaning Up Resources

After testing the application, I removed all AWS resources.

```bash
terraform destroy
```

Terraform deleted:

- ECS Cluster
- ECS Service
- Task Definition
- Amazon ECR Repository
- Related networking resources

This prevented unnecessary AWS charges.

---

# Observations

During this project, I observed that:

- Terraform modules significantly improve project organization.
- Docker provides consistent application deployment across environments.
- Amazon ECR securely stores Docker images.
- Amazon ECS simplifies container orchestration.
- AWS Fargate removes the need to manage EC2 instances.
- Infrastructure can be recreated consistently using Infrastructure as Code.

---

# Challenges Encountered

Some challenges encountered during the project included:

- Forgetting to authenticate Docker with Amazon ECR.
- Attempting to push an image before the repository existed.
- Troubleshooting ECS tasks that failed during startup.
- Understanding the relationship between ECS clusters, task definitions, and services.

Each challenge was resolved by reviewing Terraform outputs, checking AWS service logs, and verifying resource configurations.

---

# Key Concepts Learned

Through this project, I learned how to:

- Build a dynamic Node.js web application.
- Containerize applications using Docker.
- Create reusable Terraform modules.
- Provision Amazon ECR repositories.
- Push Docker images to Amazon ECR.
- Deploy applications using Amazon ECS and AWS Fargate.
- Use Terraform to automate cloud infrastructure.
- Monitor ECS deployments using CloudWatch.
- Apply Infrastructure as Code (IaC) best practices for containerized applications.

---

# Conclusion

This mini project provided practical experience deploying a modern containerized application on AWS using Terraform, Docker, Amazon ECR, and Amazon ECS. By modularizing the infrastructure, containerizing the application, storing the image in Amazon ECR, and deploying it with Amazon ECS Fargate, I successfully implemented a complete cloud-native deployment workflow.

Overall, this project reinforced key DevOps concepts, including Infrastructure as Code, containerization, cloud deployment automation, and scalable application hosting using AWS managed services.
