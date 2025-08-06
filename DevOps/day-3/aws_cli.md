 ✅ What is AWS CLI?

**AWS CLI (Command Line Interface)** is a unified tool to **manage AWS services from the terminal/shell**.

Instead of using the AWS Management Console, you can run commands to create/manage resources like EC2, S3, IAM, etc.

---

 ✅ Why Use AWS CLI?

* Automate repetitive tasks (scriptable)
* Faster than navigating through the UI
* Useful for CI/CD, scripting, monitoring
* Access to all AWS services from terminal
* Helps in managing infrastructure at scale

---

 ✅ Industry Use Cases of AWS CLI

| Use Case                        | Description                                                     |
| ------------------------------- | --------------------------------------------------------------- |
| 🔄 **Automated Deployments**    | Used in CI/CD pipelines for deploying apps (e.g., ECS, Lambda). |
| 🧪 **Dev/Test Environments**    | Automate spin-up/tear-down of test environments.                |
| 🗂️ **S3 Data Management**      | Upload/download large datasets to/from S3.                      |
| 🔐 **User and Role Management** | Create IAM users, policies, roles programmatically.             |
| ☁️ **Backup and DR**            | Create AMIs, snapshots, and backup scripts.                     |
| 📊 **Monitoring & Logging**     | Pull metrics/logs from CloudWatch.                              |
| 🧹 **Cleanup & Cost Saving**    | Stop unused EC2s or delete unattached EBS volumes.              |

---

 ✅ How to Install AWS CLI (v2)

# On **Linux**

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
sudo ./aws/install --bin-dir /usr/local/bin --install-dir /usr/local/aws-cli --update
```

# On **macOS**

```bash
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /
```

# On **Windows**

* Download the MSI installer from [AWS official site](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)

---

 ✅ Configure AWS CLI

```bash
aws configure
```

You’ll be prompted to enter:

* AWS Access Key
* AWS Secret Access Key
* Default region (e.g., us-east-1)
* Output format (json, text, table)

---

 ✅ 20 Useful AWS CLI Commands with Explanation

| #  | Command                                                                       | Purpose                                    |                                        |
| -- | ----------------------------------------------------------------------------- | ------------------------------------------ | -------------------------------------- |
| 1  | `aws s3 ls`                                                                   | List all S3 buckets.                       |                                        |
| 2  | `aws s3 ls s3://my-bucket/`                                                   | List contents of an S3 bucket.             |                                        |
| 3  | `aws s3 cp file.txt s3://my-bucket/`                                          | Upload a file to S3.                       |                                        |
| 4  | `aws s3 cp s3://my-bucket/file.txt .`                                         | Download a file from S3.                   |                                        |
| 5  | `aws s3 sync ./myfolder s3://my-bucket/`                                      | Sync a local folder to S3 recursively.     |                                        |
| 6  | `aws ec2 describe-instances`                                                  | List all EC2 instances and details.        |                                        |
| 7  | `aws ec2 start-instances --instance-ids i-1234567890abcdef0`                  | Start an EC2 instance.                     |                                        |
| 8  | `aws ec2 stop-instances --instance-ids i-1234567890abcdef0`                   | Stop an EC2 instance.                      |                                        |
| 9  | `aws ec2 terminate-instances --instance-ids i-1234567890abcdef0`              | Terminate an EC2 instance.                 |                                        |
| 10 | `aws ec2 create-snapshot --volume-id vol-12345678`                            | Create snapshot of a volume.               |                                        |
| 11 | `aws ec2 create-image --instance-id i-1234567890abcdef0 --name "MyBackupAMI"` | Create an AMI from an instance.            |                                        |
| 12 | `aws iam list-users`                                                          | List all IAM users.                        |                                        |
| 13 | `aws iam create-user --user-name newuser`                                     | Create a new IAM user.                     |                                        |
| 14 | `aws cloudwatch get-metric-data`                                              | Retrieve CloudWatch metrics.               |                                        |
| 15 | `aws lambda list-functions`                                                   | List all Lambda functions.                 |                                        |
| 16 | `aws lambda invoke --function-name my-function output.json`                   | Invoke a Lambda function manually.         |                                        |
| 17 | \`aws ecr get-login-password                                                  | docker login\`                             | Authenticate Docker with ECR registry. |
| 18 | `aws sts get-caller-identity`                                                 | Check current AWS identity (useful in CI). |                                        |
| 19 | `aws dynamodb list-tables`                                                    | List all DynamoDB tables.                  |                                        |
| 20 | `aws logs describe-log-groups`                                                | List CloudWatch Log groups.                |                                        |

---

 ✅ Bonus: AWS CLI Output Formats

You can choose output format as:

* `json` – best for scripting
* `table` – human-readable
* `text` – compact and grep-friendly

Example:

```bash
aws ec2 describe-instances --output table
```

---

 ✅ Tip: Use `--query` to filter output (JMESPath)

Example:

```bash
aws ec2 describe-instances --query "Reservations[*].Instances[*].InstanceId"
```

This helps in extracting exact values like instance IDs, names, states, etc.

---
