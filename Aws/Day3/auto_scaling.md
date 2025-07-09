
## 🔄 **What Is Auto Scaling in AWS?**

**Auto Scaling** automatically adds or removes EC2 instances **based on demand** to:

* Handle load spikes efficiently
* Save costs when demand is low
* Maintain high availability

It's powered by:

* **Launch Template/Configuration**
* **Auto Scaling Group (ASG)**
* **Scaling Policies (based on metrics)**

---

## ⚙️ **How to Configure Auto Scaling (Step-by-Step)**

### ✅ Step 1: **Create a Launch Template**

* EC2 → Launch Templates → Create new
* Define AMI, instance type, key pair, security group, user-data (e.g., install app)

---

### ✅ Step 2: **Create an Auto Scaling Group**

* EC2 → Auto Scaling Groups → Create
* Use Launch Template
* Set VPC & subnet(s)
* Set min, desired, and max instance counts (e.g., 1/2/4)
* Attach **Application Load Balancer** (optional)
* Define health check type (EC2 or ELB)

---

### ✅ Step 3: **Set Scaling Policies**

Choose scaling triggers:

* **Target Tracking**: e.g., keep average CPU at 60%
* **Step Scaling**: scale by X instances when metric crosses threshold
* **Scheduled Scaling**: based on time (e.g., weekdays 9–5)

---

## 🧪 **Test Scenario – Simulated CPU Load**

**Goal:** Test that Auto Scaling adds EC2s when CPU is high.

### 🧰 Steps:

1. Launch an ASG with min=1, max=3
2. SSH into instance and run CPU stress:

   ```bash
   sudo yum install -y stress
   stress --cpu 4 --timeout 300
   ```
3. Watch CloudWatch Metrics: CPU spikes
4. ASG detects high CPU → launches a second instance
5. After load drops, instance(s) terminate based on cooldown

> 🔍 Use **CloudWatch Alarms + Notifications** to verify event triggers.

---

## 🏢 **Industry Use Case: Scalable Web Application**

**Company**: E-commerce platform
**Problem**: Heavy load during festive sales crashes their EC2 servers
**Solution**:

* They use an **ALB + ASG** setup
* During flash sales, the ASG launches 10 more EC2s based on CPU/load
* After event ends, it scales down to 2 instances
* Saves money and ensures app stays online

---

## ✅ Summary

| Feature           | Auto Scaling Capability                   |
| ----------------- | ----------------------------------------- |
| Scale Out         | When load increases (e.g., CPU > 70%)     |
| Scale In          | When load drops (e.g., CPU < 30%)         |
| Cooldown          | Wait time between scale actions           |
| Cost Optimization | Runs only needed instances, saving budget |


