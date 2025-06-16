
## ✅ AWS Demo 4 :

### 1. **S3 – Simple Storage Service**

* What is S3? Real-life example: Google Drive for cloud.
* Demo: Create an S3 bucket, upload/download files.
* Show: Static website hosting using S3.


---

### 2. **IAM – Identity and Access Management**

* Why security matters.
* Create a new IAM user, attach permissions.
* Show: Login using new IAM user.
* Quick hands-on: Create IAM policy for read-only S3.



---

### 3. **Route 53 (Domain + DNS)**

* Explain domain name vs IP.
* Demo: Create a hosted zone, add records (A record or CNAME).
* Use-case: Point domain to S3 website or EC2 public IP.

---

### 4. **CloudWatch (Monitoring & Alerts)**

* Explain what logs and metrics are.
* Show metrics for EC2 (CPU, memory).
* Demo: Create a simple alarm to monitor CPU.

---

### 5. **RDS – Relational Database Service**

* Why managed databases?
* Demo: Create MySQL DB in Free Tier.

---

### 6. **CloudFormation (IaC Introduction)**

* Why Infrastructure as Code?
* Show: A simple YAML template that creates an S3 bucket or EC2.

---

### 7. **SNS – Simple Notification Service**

* Use case: Send email or SMS on event.
* Demo: Create topic → Subscribe email → Send a message.


__________


Perfect! Hosting a **static website using Amazon S3** is one of the best beginner-friendly AWS demos. Here's a complete **step-by-step guide** you can show to your students — it’s simple and impressive.

---

## 🌐 Static Website Hosting on S3 – Step-by-Step

### 🧠 What is a Static Website?

A static website contains HTML, CSS, JS files — no backend server logic (e.g., portfolio, landing page, blogs).

---

## ✅ Demo to Host Static Website on S3:

---

### 🔹 Step 1: Prepare Your Website Files

Make sure you have:

* `index.html` (home page)
* `error.html` (optional, for 404 page)

👉 Example:

```html
<!-- index.html -->
<html>
  <head><title>My S3 Website</title></head>
  <body><h1>Welcome to My AWS Static Website</h1></body>
</html>
```

---

### 🔹 Step 2: Create an S3 Bucket

1. Go to **S3 service** in AWS Console
2. Click **Create bucket**
3. Bucket name: **must be unique globally**
4. **Uncheck**: "Block all public access"
5. Click **Create bucket**

---

### 🔹 Step 3: Upload Website Files

1. Open your newly created bucket
2. Click **Upload**
3. Select `index.html`, `error.html`, etc.
4. Click **Upload**

---

### 🔹 Step 4: Enable Static Website Hosting

1. Go to **Properties** tab of the bucket
2. Scroll to **Static website hosting**
3. Select: **Enable**
4. Choose:

   * Index document: `index.html`
   * Error document: `error.html`
5. Click **Save**

👉 You’ll get a **Website endpoint URL**, e.g.

```
http://your-bucket-name.s3-website-us-east-1.amazonaws.com
```

---

### 🔹 Step 5: Make Files Public (Read Access)

1. Go to **Permissions** tab
2. Click **Bucket Policy**
3. Paste this policy (replace `your-bucket-name`):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```

4. Click **Save**

✅ Now your files are **publicly accessible** via browser.

---

### ✅ Final Output

Go to the **Website URL** provided → you’ll see your `index.html` webpage live!

---
