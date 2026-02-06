

```markdown
# Bash Infrastructure Deployment Script

This script is designed to automate the process of setting up cloud infrastructure across different environments (local, testing, production) using AWS CLI. It performs environment validation, checks for necessary tools, and creates EC2 instances and S3 buckets based on specified parameters.

---

## Script Breakdown

### Shebang
```bash
#!/bin/bash
```
- Specifies that the script should run using Bash shell.

---

### Environment Variable
```bash
ENVIRONMENT=$1
```
- Captures the first command-line argument to determine which environment the script should operate in.

---

### Function: Check Number of Arguments
```bash
check_num_of_args() {
  # Checking the number of arguments
  if [ "$#" -ne 0 ]; then
    echo "Usage: $0 <environment>"
    exit 1
  fi
}
```
- Ensures no extra arguments are passed.
- Usage: Run as `./script.sh <environment>`

---

### Function: Activate Infrastructure Environment
```bash
activate_infra_environment() {
  # Acting based on the argument value
  if [ "$ENVIRONMENT" == "local" ]; then
    echo "Running script for Local Environment..."
  elif [ "$ENVIRONMENT" == "testing" ]; then
    echo "Running script for Testing Environment..."
  elif [ "$ENVIRONMENT" == "production" ]; then
    echo "Running script for Production Environment..."
  else
    echo "Invalid environment specified. Please use 'local', 'testing', or 'production'."
    exit 2
  fi
}
```
- Checks the value of `$ENVIRONMENT` and prints a message accordingly.
- Exits if an invalid environment is specified.

---

### Function: Check if AWS CLI is Installed
```bash
check_aws_cli() {
  if ! command -v aws &> /dev/null; then
    echo "AWS CLI is not installed. Please install it before proceeding."
    return 1
  fi
}
```
- Validates AWS CLI installation.

---

### Function: Check AWS Profile
```bash
check_aws_profile() {
  if [ -z "$AWS_PROFILE" ]; then
    echo "AWS profile environment variable is not set."
    return 1
  fi
}
```
- Checks if the `AWS_PROFILE` environment variable is set, which is needed for authentication.

---

### Function: Create EC2 Instances
```bash
create_ec2_instances() {
  # Specify the parameters for the EC2 instances
  instance_type="t2.micro"
  ami_id="ami-0cd59ecaf368e5ccf"  # Replace with your AMI ID
  count=2  # Number of instances to create
  region="eu-west-2" # AWS region

  # Create the EC2 instances
  aws ec2 run-instances \
      --image-id "$ami_id" \
      --instance-type "$instance_type" \
      --count $count \
      --key-name MyKeyPair

  # Check if the EC2 instances were created successfully
  if [ $? -eq 0 ]; then
    echo "EC2 instances created successfully."
  else
    echo "Failed to create EC2 instances."
  fi
}
```
- Creates 2 EC2 instances with specified parameters.
- You need to replace `ami-0cd59ecaf368e5ccf` with a valid AMI ID for your region.

---

### Function: Create S3 Buckets
```bash
create_s3_buckets() {
  # Define a company name as prefix
  company="datawise"
  # Array of department names
  departments=("Marketing" "Sales" "HR" "Operations" "Media")
  
  # Loop through departments and create buckets
  for department in "${departments[@]}"; do
    bucket_name="${company}-${department}-Data-Bucket"
    # Create S3 bucket in specified region
    aws s3api create-bucket --bucket "$bucket_name" --region your-region
    if [ $? -eq 0 ]; then
      echo "S3 bucket '$bucket_name' created successfully."
    else
      echo "Failed to create S3 bucket '$bucket_name'."
    fi
  done
}
```
- Creates S3 buckets for different departments with a naming convention.
- Replace `your-region` with your AWS region, e.g., `eu-west-2`.

---

## How to Run the Script

```bash
chmod +x your_script_name.sh
./your_script_name.sh <environment>
```

- Example:
```bash
./deploy_infra.sh production
```

## Final Notes
- Ensure your AWS credentials and permissions are configured correctly before running this script.
- Replace placeholder values like AMI IDs and regions with values suitable for your AWS account.

---

## Summary
- Validates CLI tools and environment variables.
- Sets up AWS EC2 instances.
- Creates multiple S3 buckets for organizational purposes.
- Designed for easy customization and automation in DevOps workflows.
```

You can copy this entire block into a `.md` file (e.g., `README.md`) on GitHub. Let me know if you'd like me to customize or expand on any part!
