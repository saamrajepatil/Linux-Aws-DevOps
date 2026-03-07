Configuring the AWS CLI
Before configuration, ensure the AWS CLI is installed on your system using the official AWS documentation. 
Open your terminal or command prompt.

Run the aws configure command.

Step 1: Install AWS CLI
Ensure that you have the AWS CLI installed on your local machine. You can download and install it from the official AWS CLI download page.

Step 2: Open a Terminal or Command Prompt
Open your terminal or command prompt on your local machine.

Step 3: Run aws configure Command
In the terminal, run the following command:

aws configure

Step 4: Enter AWS Access Key ID
Enter the AWS Access Key ID associated with your IAM user.

Step 5: Enter AWS Secret Access Key
Enter the AWS Secret Access Key associated with your IAM user.

Step 6: Set Default Region
Enter the default region you want to use.

Step 7: Set Output Format
Choose the default output format.

Note: (the values are stored in ~/.aws/credentials and ~/.aws/config files):



<img width="720" height="128" alt="image" src="https://github.com/user-attachments/assets/63260547-45fc-4401-bc29-06e1b430a06c" />



20 Common AWS CLI Commands 
Here are 20 common AWS CLI commands across various services:
General and Account Management
aws --version: Check the installed AWS CLI version.

aws configure list: View your current AWS CLI configuration settings.

aws sts get-caller-identity: Display the IAM user or role identity currently in use.

aws iam list-users: List all IAM users in your AWS account.

aws ec2 describe-regions: List all available AWS regions. 


Amazon S3 (Simple Storage Service) 
aws s3 ls: List all S3 buckets in your account.
aws s3 mb s3://<bucket-name>: Make (create) a new S3 bucket.
aws s3 cp <local-file> s3://<bucket-name>/: Copy a file from your local system to an S3 bucket.
aws s3 sync <local-directory> s3://<bucket-name>/: Sync an entire directory to an S3 bucket.
aws s3 rm s3://<bucket-name>/<file-name>: Delete an object from a bucket. 

Amazon EC2 (Elastic Compute Cloud) 
aws ec2 describe-instances: Describe all EC2 instances (can use filters to narrow results).
aws ec2 start-instances --instance-ids <instance-id>: Start a specific EC2 instance.
aws ec2 stop-instances --instance-ids <instance-id>: Stop a specific EC2 instance.
aws ec2 create-key-pair --key-name <key-name>: Create a new EC2 key pair.
aws ec2 describe-security-groups: View details about your security groups. 

Other Services
aws lambda list-functions: List all Lambda functions in the configured region.
aws rds describe-db-instances: List information about RDS database instances.
aws cloudwatch list-metrics: List available CloudWatch metrics.
aws sns list-topics: List all Amazon SNS topics.
aws sqs list-queues: List all Amazon SQS queues
