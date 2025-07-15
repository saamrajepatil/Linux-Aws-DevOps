

## 🛡️ 1. **AWS Shield**

### ✅ What is AWS Shield?

**AWS Shield** is a managed **DDoS protection service** that safeguards your AWS applications.

### 🔄 Types:

* **Shield Standard** (Free): Automatically protects ALB, CloudFront, Route 53, and Global Accelerator from common DDoS attacks.
* **Shield Advanced** (Paid): Enhanced protection + 24x7 DDoS Response Team + cost protection.

### 🎯 Use Case:

You run a global e-commerce website using CloudFront and ALB. AWS Shield protects you from volumetric DDoS attacks (e.g., SYN flood, UDP reflection).

### 📌 Example:

No setup needed for **Shield Standard**. It auto-enables on:

* **ALB**
* **CloudFront**
* **Global Accelerator**
* **Route 53**

For **Shield Advanced**, subscribe and associate it with specific resources.

---

## 🧠 2. **AWS GuardDuty**

### ✅ What is GuardDuty?

**GuardDuty** is an **intelligent threat detection** service that continuously monitors:

* VPC flow logs
* DNS logs
* CloudTrail events

It identifies:

* Reconnaissance (port scanning)
* Credential theft
* Unusual data access
* Malicious EC2 or IAM activity

### 🎯 Use Case:

You're running a financial app. GuardDuty detects suspicious activity like:

* EC2 calling crypto mining domains
* IAM role making API calls from unusual locations

### 📌 How to Use:

1. Go to **GuardDuty Console**
2. Click **Enable GuardDuty**
3. It will start analyzing logs from:

   * CloudTrail
   * VPC Flow Logs
   * DNS logs

### 🧾 Example Finding:

> "An EC2 instance i-123456 is querying a known malicious domain."

Then you can take action (quarantine, isolate, rotate keys).

---

## 🔐 3. **AWS Secrets Manager**

### ✅ What is Secrets Manager?

AWS **Secrets Manager** stores, rotates, and manages **secrets** like:

* Database credentials
* API keys
* OAuth tokens

Unlike storing in EC2/user data, Secrets Manager **encrypts and rotates secrets automatically**.

### 🎯 Use Case:

You deploy an app on EC2/ECS that connects to RDS. Instead of hardcoding DB credentials, store them in Secrets Manager and retrieve them securely in runtime.

### 📌 How to Use:

1. Go to **Secrets Manager**
2. Click **Store a new secret**
3. Choose secret type (e.g., RDS credentials)
4. Enter key-value pair (e.g., `username=admin`, `password=xxxx`)
5. Give secret name (`prod/db-secret`)
6. Enable automatic rotation (optional)

### 🧾 Example Access via Python (boto3):

```python
import boto3
import json

client = boto3.client('secretsmanager')
response = client.get_secret_value(SecretId='prod/db-secret')
secret = json.loads(response['SecretString'])

print(secret['username'], secret['password'])
```

---

## 🔒 4. **AWS Certificate Manager (ACM)**

### ✅ What is ACM?

**ACM** lets you provision, manage, and deploy **SSL/TLS certificates** on AWS services — mainly for **HTTPS and encryption in transit**.

### 🎯 Use Case:

You run a public website on **ALB or CloudFront** and want HTTPS enabled. Use ACM to issue a free SSL certificate.

### 📌 How to Use:

1. Go to **Certificate Manager**
2. Click **Request a certificate**
3. Choose **Public certificate**
4. Add domain (e.g., `www.example.com`)
5. Validate via DNS or email (Route 53 makes DNS easy)
6. Attach to CloudFront or ALB

✅ Works seamlessly with:

* Elastic Load Balancer (ALB/ELB)
* CloudFront
* API Gateway
* App Runner

> ⚠️ Note: Free ACM certificates are only valid for domains in use with **AWS services**.

---

## 🔁 Summary Table

| Service             | Purpose                     | Example Use Case                         |
| ------------------- | --------------------------- | ---------------------------------------- |
| **Shield**          | DDoS protection             | Protect ALB from DDoS attack             |
| **GuardDuty**       | Threat detection            | Detect EC2 accessing malware domain      |
| **Secrets Manager** | Manage and rotate secrets   | Store and rotate DB credentials securely |
| **ACM**             | Manage SSL/TLS certificates | Enable HTTPS on CloudFront and ALB       |


