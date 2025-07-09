

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

---


## 🔹 Application Load Balancer (ALB)

### 1. **What is ALB and what layer does it operate on?**

**Answer:** ALB is a Layer 7 load balancer that supports HTTP and HTTPS protocols. It routes requests based on host headers, paths, methods, and more.

### 2. **How does path-based routing work?**

**Answer:** You define rules in ALB listener: e.g., `/images` → Image-Target Group, `/api` → API-Target Group.

### 3. **What is host-based routing?**

**Answer:** ALB uses the `Host` header to route requests. For instance, `api.example.com` routes to API backend, `shop.example.com` to storefront.

### 4. **Can ALB handle WebSocket and HTTP/2?**

**Answer:** Yes, ALB supports both WebSocket and HTTP/2 traffic.

### 5. **What is a target group?**

**Answer:** A target group keeps track of instances, IPs, or containers for ALB to distribute traffic and perform health checks.

### 6. **Explain ALB health checks.**

**Answer:** Configurable per target group (interval, path, threshold). Unhealthy targets are removed from rotation until healthy again.

### 7. **How do you secure ALB?**

**Answer:** Use TLS listeners, integrate with ACM for TLS certs, configure security groups, enable WAF and Shield for protection.

### 8. **What are ALB listener rules?**

**Answer:** Defined in listener config, they include conditions (path, host, headers) and actions (forward, redirect, fixed response).

### 9. **ALB vs NLB – differences?**

**Answer:** ALB = Layer 7, supports HTTP routing. NLB = Layer 4, handles TCP/UDP, preserves source IP, better for high throughput.

### 10. **Can ALB send to Lambda functions?**

**Answer:** Yes! ALB supports invoking Lambda directly via Lambda target groups.

### 11. **What is sticky session in ALB?**

**Answer:** Application-controlled session affinity via cookies. Not recommended in microservices but used for stateful apps.

### 12. **How to perform blue/green deployments with ALB?**

**Answer:** Create two target groups (blue & green), switch listener rule to direct live traffic to green group for the cutover.

### 13. **Explain cross-zone load balancing.**

**Answer:** ALB can distribute traffic evenly across AZs even if instances count per AZ differs.

### 14. **Can ALB scale automatically?**

**Answer:** Yes. ALB scales behind the scenes based on traffic; no manual action required.

### 15. **What’s URL-based redirect in ALB?**

**Answer:** Using listener rule action "redirect" to forward requests (e.g., HTTP → HTTPS) or to different URLs using regex.

---

## 🔹 Auto Scaling (ASG)

### 16. **What is Auto Scaling Group?**

**Answer:** ASG manages EC2 fleet, handling scale-out/in based on policies, scheduled, or on-demand scaling.

### 17. **What is a Launch Template?**

**Answer:** Immutable config that defines instance type, AMI, key pair, security groups, user data for ASG and other services.

### 18. **Different types of scaling policies?**

**Answer:**

* Target Tracking (maintain a metric, e.g., avg CPU 50%)
* Step Scaling (scale in stages based on thresholds)
* Scheduled (scale at specific times)

### 19. **What is the cooling period?**

**Answer:** Time between scaling activities to prevent rapid scale fluctuations.

### 20. **What metrics are commonly used to scale EC2s?**

**Answer:** CPU utilization, network IO, Request count per target, custom CloudWatch metrics.

### 21. **How to handle scale-in protection?**

**Answer:** Enable instance protection to prevent specific instances from termination during scale in.

### 22. **Spot Instances with ASG – possible?**

**Answer:** Yes, ASG supports spot instances and mixed instance policies for cost efficiency.

### 23. **Scheduled scaling use case?**

**Answer:** Known traffic increase during business hours — ASG scales out before and scales in after.

### 24. **What is instance warm-up?**

**Answer:** A period where new instances are assumed to be initializing before considering CloudWatch metrics.

### 25. **Can ASG attach to ALB?**

**Answer:** Yes — create ASG and specify target groups in ALB listener to register instances automatically.

---

## 🔄 Integration & Testing

### 26. **How do you test Auto Scaling?**

**Answer:** Use `stress` tool to simulate load, watch CloudWatch metrics crossing thresholds triggering scale events and verify instance count changes.

### 27. **How do you test ALB routing decisions?**

**Answer:** Access URLs configured in listener rules, e.g., `/api` routes to API backend. Check health status in AWS Console.

### 28. **Real-world use case?**

**Answer:** E-commerce app. ALB routes traffic to front-end and checkout services. Under flash sale, ASG scales out web servers; auto scale-in post-event saves cost, ALB balances across AZs.

### 29. **What’s cost impact of Auto Scaling + ALB?**

**Answer:** ALB incurs per-hour and per-GB cost; ASG optimizes by scaling instances before and after peak, reducing idle hours.

### 30. **Troubleshooting steps during scale failure?**

**Answer:** Check health checks, ASG events, CloudWatch alarms. Verify launch template parameters, subnet availability, security group inbound limits.

---



