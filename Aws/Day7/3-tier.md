## 🧱 3-Tier Architecture (Compute-Based)

### ✅ Definition:

A **3-tier architecture** splits your application into **three logical layers**:

1. **Presentation Tier (Frontend)**
2. **Application Tier (Backend logic)**
3. **Data Tier (Database)**

> All components run on compute services (like EC2 or Auto Scaling groups).
>
> <img width="1100" height="550" alt="image" src="https://github.com/user-attachments/assets/b206d823-a8b6-46b7-85ba-cc4808364b0c" />


<img width="1022" height="1051" alt="image" src="https://github.com/user-attachments/assets/11a06005-19b1-49ab-b8e5-9b2e7d51cd1e" />

---

## 🧩 Architecture Components

| Tier                         | Description                              | AWS Services (Compute-Based)                                  |
| ---------------------------- | ---------------------------------------- | ------------------------------------------------------------- |
| **Presentation (Web Tier)**  | Handles user interface and user requests | EC2 (with ALB), Auto Scaling, CloudFront (optional), Route 53 |
| **Application (Logic Tier)** | Business logic, API processing           | EC2 (App servers), Load Balancer, Auto Scaling                |
| **Database Tier**            | Persistent storage of data               | Amazon RDS, Aurora, or MySQL/PostgreSQL on EC2                |

---

## 🖼️ Architecture Diagram (Text Representation)

```
                +-----------------------------+
                |        Route 53 / DNS       |
                +-----------------------------+
                            |
                            ▼
                +-----------------------------+
                |       CloudFront (optional) |
                +-----------------------------+
                            |
                            ▼
                +-----------------------------+
                |  ALB (Web Load Balancer)     |
                +-----------------------------+
                            |
             +--------------+--------------+
             |                             |
       +-----------+                +-----------+
       |  EC2 #1   |  <- Web Tier   |  EC2 #2   |
       +-----------+                +-----------+
             |                             |
             +--------------+--------------+
                            ▼
                +-----------------------------+
                |   ALB (App Load Balancer)   |
                +-----------------------------+
                            |
             +--------------+--------------+
             |                             |
       +-----------+                +-----------+
       |  EC2 #3   |  <- App Tier   |  EC2 #4   |
       +-----------+                +-----------+
                            |
                            ▼
                +-----------------------------+
                |     Amazon RDS (DB Tier)     |
                +-----------------------------+
```

---

## 🔐 Security Layers (Best Practices)

* **NACLs / Security Groups**:

  * Only ALB accessible from the internet (port 80/443)
  * App tier only open to Web tier SG
  * DB tier only open to App tier SG

* **Private Subnets** for App & DB tiers

* **Public Subnet** only for Load Balancer

---

## 📦 Storage and Deployment Options

| Layer    | Deployment Tools                            |
| -------- | ------------------------------------------- |
| Web Tier | AMIs, EC2 + Auto Scaling, Elastic Beanstalk |
| App Tier | CodeDeploy, Jenkins, Ansible, CI/CD         |
| DB Tier  | RDS Multi-AZ, Read Replicas, Backup enabled |

---

## 🎯 Use Case Example

* E-Commerce site:

  * Web Tier: HTML/React site served from EC2
  * App Tier: Java Spring Boot or Node.js APIs
  * DB Tier: RDS MySQL/Aurora for orders, users, etc.

---

## 🔁 Alternatives & Variations

| Alternative                                  | When to Use                              |
| -------------------------------------------- | ---------------------------------------- |
| Fargate / ECS                                | For containerized compute instead of EC2 |
| Serverless (Lambda + API Gateway + DynamoDB) | For scalable, event-driven apps          |
| Kubernetes (EKS)                             | For advanced container orchestration     |

