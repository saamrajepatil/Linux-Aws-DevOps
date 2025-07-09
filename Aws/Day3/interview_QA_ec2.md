

### **1. What is EC2 in AWS?**

**Answer:**
EC2 (Elastic Compute Cloud) is a web service that provides resizable compute capacity (virtual servers) in the cloud. It allows you to run Linux or Windows-based instances and scale them on-demand.

---

### **2. What are EC2 instance types?**

**Answer:**
EC2 instance types vary by use case:

* General Purpose (e.g., t3, m5)
* Compute Optimized (e.g., c6)
* Memory Optimized (e.g., r5)
* Storage Optimized (e.g., i3)
* Accelerated (e.g., p3, g4)

---

### **3. What are Security Groups in EC2?**

**Answer:**
Security Groups are virtual firewalls that control inbound/outbound traffic for EC2 instances. They work at the instance level and are stateful.

---

### **4. What is the difference between a Security Group and a Network ACL?**

**Answer:**

* **Security Group**: Instance-level, stateful, allows rules only.
* **NACL**: Subnet-level, stateless, supports allow and deny rules.

---

### **5. What is a Key Pair in EC2?**

**Answer:**
A key pair consists of a public and private key used to SSH into an EC2 instance securely. AWS stores the public key; you store the private key.

---

### **6. What is EBS in EC2?**

**Answer:**
Elastic Block Store (EBS) is block-level storage for EC2. It is persistent and can be attached/detached from instances.

---

### **7. What is the difference between EBS and Instance Store?**

**Answer:**

* **EBS**: Persistent, survives instance stop/start.
* **Instance Store**: Ephemeral, data is lost when the instance stops or is terminated.

---

### **8. What is an AMI?**

**Answer:**
AMI (Amazon Machine Image) is a pre-configured template for launching EC2 instances. It includes OS, software, and configuration.

---

### **9. How do you scale EC2 instances automatically?**

**Answer:**
Use **Auto Scaling Groups (ASG)**, which scale EC2 capacity based on metrics like CPU, memory, or load balancer health checks.

---

### **10. What are EC2 instance states?**

**Answer:**

* Pending
* Running
* Stopping
* Stopped
* Terminated
* Shutting down

---

### **11. What is EC2 Spot Instance?**

**Answer:**
Spot Instances use spare AWS capacity at up to 90% discount but can be terminated at any time if AWS needs the capacity.

---

### **12. Can you stop and restart a Spot Instance?**

**Answer:**
No. Spot instances may be interrupted by AWS. However, **Spot Fleets with hibernation support** can resume state under certain conditions.

---

### **13. What is user-data in EC2?**

**Answer:**
User-data is a script or cloud-init configuration that runs at instance launch for bootstrapping tasks (e.g., installing software).

---

### **14. How do you connect to a Linux EC2 instance?**

**Answer:**
Use SSH:

```bash
ssh -i my-key.pem ec2-user@<public-ip>
```

---

### **15. How do you enable detailed monitoring for EC2?**

**Answer:**
In the EC2 console or via CLI, enable **CloudWatch Detailed Monitoring** to get 1-minute metrics (vs default 5-min).

---

### **16. What is an Elastic IP?**

**Answer:**
A static IPv4 address provided by AWS for dynamic cloud computing. You can associate/disassociate it with EC2 instances.

---

### **17. What is the difference between public and private IP in EC2?**

**Answer:**

* **Public IP**: Accessible from the internet
* **Private IP**: Only accessible within the VPC

---

### **18. What happens when you terminate an EC2 instance?**

**Answer:**
The instance is permanently deleted, and all data on **instance store volumes is lost**. EBS volumes are also deleted unless flagged to persist.

---

### **19. How do you create a custom AMI from an EC2 instance?**

**Answer:**

* Stop the instance (optional)
* Choose **Create Image** from EC2 console
* AWS creates an AMI and associated snapshots

---

### **20. How do you schedule EC2 start/stop automatically?**

**Answer:**
Use **AWS Lambda + CloudWatch Events** or **AWS Instance Scheduler solution** to automate EC2 lifecycle actions.

