### ✅ **Day 3 – AWS Demo: **

---

### 🔹 1. **What is AWS? **

* Amazon Web Services = On-demand cloud platform.
* Offers services like compute (EC2), storage (S3), database (RDS), etc.
* Pay-as-you-go model.

🧠 **real-life analogy**:
*"Like renting a hotel room instead of building a house."*

---

![image](https://github.com/user-attachments/assets/30d4a4b2-a864-443f-a4bc-6bb4174620f9)



### 🔹 2. **Global Infrastructure **

* **Region** = A geographical location (e.g., Mumbai).
* **Availability Zones (AZs)** = Data centers in a region.
* **Edge Locations** = For caching (CloudFront/CDN).

---

## ✅ **1. Region**

### 📘 Definition:

A **Region** is a **geographic area** where AWS has multiple data centers (called Availability Zones).

### 🌍 Examples:

* `us-east-1` → N. Virginia, USA
* `ap-south-1` → Mumbai, India
* `eu-west-1` → Ireland

### 🧠 Real-Life Example:

Imagine **Regions as different cities**.
Just like you can open a branch office in Mumbai, Delhi, or New York — AWS has data centers in different parts of the world called regions.

---

## ✅ **2. Availability Zone (AZ)**

### 📘 Definition:

An **Availability Zone** is one or more **physically separated data centers** within a Region. Each region has 2 or more AZs.

### 🌍 Example:

* Mumbai Region (`ap-south-1`) has:

  * `ap-south-1a`
  * `ap-south-1b`
  * `ap-south-1c`

### 🧠 Real-Life Example:

If **Mumbai is a Region**, then **Andheri, Navi Mumbai, and Thane** are the AZs — different buildings with internet and power backup, but all located in Mumbai.

---

## ✅ **3. VPC (Virtual Private Cloud)**

### 📘 Definition:

A **VPC** is your own **isolated network** in AWS where you can launch your resources like EC2, RDS, etc.

### 🌐 Example:

* Your VPC has:

  * CIDR range: `10.0.0.0/16`
  * 2 public and 2 private subnets

### 🧠 Real-Life Example:

Think of VPC like your **own private office building** inside a city (AWS). You decide:

* Who can enter (firewalls)
* How rooms are separated (subnets)
* Internet access (gateways)

---

## ✅ **4. Subnet**

### 📘 Definition:

A **Subnet** is a **smaller network** inside a VPC. You divide your VPC into subnets to organize and secure your resources.

### 🔀 Types:

* **Public Subnet**: Has internet access
* **Private Subnet**: No direct internet access

### 🧠 Real-Life Example:

Inside your office (VPC), subnets are like **different departments**:

* Reception (public subnet) — accessible to outsiders
* HR/Accounts (private subnet) — only internal access

---

## ✅ **5. Security Group**

### 📘 Definition:

A **Security Group** is like a **firewall** that controls traffic **to and from** your EC2 instances.

### 🚦 Rules:

* Inbound Rules (who can access)
* Outbound Rules (where instance can go)

### 🧠 Real-Life Example:

Think of Security Groups as **guards at office doors**:

* Only allow visitors (port 22 for SSH, port 80 for web)
* Don’t let employees go outside unless allowed

### 🔐 Example:

Allow:

* SSH from your IP → `22`
* HTTP from anywhere → `80`
* HTTPS from anywhere → `443`

---

## 🧪 Bonus Visual Analogy Summary:

| AWS Term          | Real-Life Analogy                     |
| ----------------- | ------------------------------------- |
| Region            | A city (e.g., Mumbai)                 |
| Availability Zone | Different data centers in that city   |
| VPC               | Your own office building in that city |
| Subnet            | Rooms/departments inside your office  |
| Security Group    | Security guards at office doors       |


---

### 🔹 3. **Hands-On: Create EC2 Instance **

Show how to:

* Go to **EC2 → Launch Instance**
* Choose Amazon Linux 2 AMI or Ubuntu
* t2.micro (Free tier)
* Create new Key Pair
* Configure Security Group (allow port 22 – SSH)
* Launch and Connect using SSH (Git Bash)

---

### 🔹 4. **Intro to S3 (Simple Storage Service) **

* Object storage – store images, PDFs, backups.
* **Create a Bucket**
* Upload a file
* Make it public

🎯 Real-world: Hosting a static website, image sharing, logs storage.

---

## ✅ **Demo: Install Web Server on EC2 and Access in Browser**

### 🛠️ Step-by-Step (Using Amazon Linux 2)

### **1. Launch an EC2 Instance**

* Choose **Amazon Linux 2 AMI**
* Instance type: `t2.micro` (Free tier)
* Select/Create **key pair**
* Create a new **security group**:

  * Allow **SSH (port 22)** from your IP
  * Allow **HTTP (port 80)** from **Anywhere (0.0.0.0/0)**

---

### **2. Connect to EC2**

In terminal (Git Bash or VSCode terminal):

```bash
ssh -i "your-key.pem" ec2-user@<public-ip>
```

---

### **3. Install Web Server (Choose One)**

#### 👉 **: Install Apache HTTPD**

```bash
sudo yum update -y
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
```

### **4. Test the Web Page**

* Go to your **EC2 Public IP** in the browser:
  👉 `http://<your-public-ip>`

You’ll see:


* HTTPD: Apache default test page

---

### ✅ **5. Optional – Customize the Web Page**

```bash
echo '<h1>Hello from EC2 Web Server!</h1>' | sudo tee /var/www/html/index.html
```

Then refresh the browser to see your custom message.

---

### 💡 Tips:

* how EC2 + Security Group + Web Server + Public IP = Cloud Website
* Highlight importance of **port 80 open** for web traffic
*  real websites need **domain + SSL** later

---


### 🔹 6. **Fun Facts**

* AWS is used by Netflix, NASA, Zomato, Swiggy
* AWS skills = good salary + job demand
* Many companies use AWS with DevOps

---
