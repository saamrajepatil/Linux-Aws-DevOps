# What will you learn 

## Introduction to EC2:

What is EC2, and why is it important?

```
- Amazon Elastic Compute Cloud (Amazon EC2) is a web service that provides secure, resizable compute capacity in the cloud.
- Access reliable, scalable infrastructure on demand. Scale capacity within minutes with SLA commitment of 99.99% availability.
- Provide secure compute for your applications. Security is built into the foundation of Amazon EC2 with the AWS Nitro System.
- Optimize performance and cost with flexible options like AWS Graviton-based instances, Amazon EC2 Spot instances, and AWS Savings Plans.
```

EC2 usecases

```
Deliver secure, reliable, high-performance, and cost-effective compute infrastructure to meet demanding business needs.
Access the on-demand infrastructure and capacity you need to run HPC applications faster and cost-effectively.
Access environments in minutes, dynamically scale capacity as needed, and benefit from AWS’s pay-as-you-go pricing.
Deliver the broadest choice of compute, networking (up to 400 Gbps), and storage services purpose-built to optimize price performance for ML projects
```

EC2 Instance Types

Recommended to follow [this](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html) page for very detailed and updated information.

General purpose

```
General Purpose instances are designed to deliver a balance of compute, memory, and network resources. They are suitable for a wide range of applications, including web servers,
small databases, development and test environments, and more.
```

Compute optimized

```
Compute Optimized instances provide a higher ratio of compute power to memory. They excel in workloads that require high-performance processing such as batch processing, 
scientific modeling, gaming servers, and high-performance web servers.
```

Memory optimized

```
Memory Optimized instances are designed to handle memory-intensive workloads. They are suitable for applications that require large amounts of memory, such as in-memory databases,
real-time big data analytics, and high-performance computing.
```

Storage optimized

```
Storage Optimized instances are optimized for applications that require high, sequential read and write access to large datasets. 
They are ideal for tasks like data warehousing, log processing, and distributed file systems.
```

Accelerated computing

```
Accelerated Computing Instances typically come with one or more types of accelerators, such as Graphics Processing Units (GPUs),
Field Programmable Gate Arrays (FPGAs), or custom Application Specific Integrated Circuits (ASICs). 
These accelerators offload computationally intensive tasks from the main CPU, enabling faster and more efficient processing for specific workloads.
```

![image](https://github.com/iam-veeramalla/aws-devops-zero-to-hero/assets/43399466/fc8e083c-dba5-41a6-94b9-14ebef0255c1)

Instance families

```
    C – Compute

    D – Dense storage

    F – FPGA

    G – GPU

    Hpc – High performance computing

    I – I/O

    Inf – AWS Inferentia

    M – Most scenarios

    P – GPU

    R – Random access memory

    T – Turbo

    Trn – AWS Tranium

    U – Ultra-high memory

    VT – Video transcoding

    X – Extra-large memory
```

Additional capabilities

```
    a – AMD processors

    g – AWS Graviton processors

    i – Intel processors

    d – Instance store volumes

    n – Network and EBS optimized

    e – Extra storage or memory

    z – High performance

---

## ✅ **1. Instance Types (Based on Use Case / Size)**

These are grouped by **family** — each family optimized for a specific use:

### 🧮 **a. General Purpose**

| Family            | Examples                      | Use Case               |
| ----------------- | ----------------------------- | ---------------------- |
| `t4g`, `t3`, `t2` | Burstable (low-cost dev/test) | Small apps, websites   |
| `m7`, `m6`, `m5`  | Balanced compute/memory       | App servers, small DBs |

---

### ⚙️ **b. Compute Optimized**

| Family           | Examples        | Use Case                      |
| ---------------- | --------------- | ----------------------------- |
| `c7`, `c6`, `c5` | High CPU\:ratio | Web servers, batch processing |

---

### 🧠 **c. Memory Optimized**

| Family           | Examples                   | Use Case                          |
| ---------------- | -------------------------- | --------------------------------- |
| `r7`, `r6`, `r5` | Memory-intensive workloads | In-memory DBs, caching, analytics |
| `x2`, `x1`       | Extra-large memory         | SAP HANA, real-time big data      |
| `z1d`            | High CPU + memory          | EDA, high-end apps                |

---

### 💽 **d. Storage Optimized**

| Family           | Examples             | Use Case                           |
| ---------------- | -------------------- | ---------------------------------- |
| `i3`, `i4`, `d3` | Fast local NVMe/SSD  | NoSQL DBs, Elasticsearch, big data |
| `h1`             | High disk throughput | Data warehousing, log processing   |

---

### 🎮 **e. Accelerated Computing**

| Family     | Examples                  | Use Case                           |
| ---------- | ------------------------- | ---------------------------------- |
| `p4`, `p3` | GPU (NVIDIA) for ML, AI   | Deep learning, HPC                 |
| `g5`, `g4` | GPU for graphics, gaming  | Video rendering, streaming         |
| `inf1`     | ML inference acceleration | TensorFlow inference, real-time AI |

---

## 💰 **2. Purchase Options (Based on Cost Plan)**

AWS offers different **payment models**:

---

### 🟢 **a. On-Demand Instances**

* **Pay per second/hour**
* **No commitment**
* Best for short-term, unpredictable workloads

> 🧠 Example: Start a dev/test server and stop anytime

---

### 🟠 **b. Reserved Instances (RI)**

* Commit to **1 or 3 years**
* **Up to 75% cheaper** than on-demand
* Can be **Standard** (locked) or **Convertible** (change type)

> 💼 Example: A production web server you’ll use 24/7 for 1+ years

---

### 🔄 **c. Spot Instances**

* Use **unused EC2 capacity**
* **Up to 90% cheaper**, but can be terminated anytime
* Best for **batch jobs**, CI/CD, rendering

> 🧪 Example: Use spot instances to train ML models or run nightly builds

---

### 🟡 **d. Savings Plans**

* Flexible alternative to RIs
* Commit to **spend (\$/hr)** instead of locking to a specific instance
* Apply across EC2, Lambda, Fargate

> 💸 Example: You spend \$100/hour on compute — AWS gives up to 66% discount

---

### ⚡ **e. Dedicated Hosts / Instances**

* Physical server dedicated to your workloads
* Required for **BYOL (Bring Your Own License)** like Oracle, SQL Server

---

### 📦 **Summary Table**

| Type              | Use Case                | Example Families |
| ----------------- | ----------------------- | ---------------- |
| General Purpose   | Balanced workloads      | t4g, m6          |
| Compute Optimized | CPU-heavy tasks         | c6, c7           |
| Memory Optimized  | In-memory DBs, big data | r6, x2           |
| Storage Optimized | High IOPS / local disk  | i3, i4           |
| GPU Instances     | ML, AI, video rendering | p4, g5           |

| Pricing Plan       | Best For                             | Notes                                  |
| ------------------ | ------------------------------------ | -------------------------------------- |
| On-Demand          | Unpredictable use                    | Most flexible, no discount             |
| Reserved Instances | Steady, long-term workloads          | 1–3 year commitment                    |
| Spot Instances     | Non-critical, fault-tolerant tasks   | Cheapest, but may be interrupted       |
| Savings Plans      | Long-term, flexible spend commitment | Applies across instance types/services |
| Dedicated Hosts    | Licensing or compliance needs        | Full physical isolation                |

---

## EC2 Instance Basics:

Understanding the concept of virtual servers and instances.
Key components of an EC2 instance: AMI (Amazon Machine Image), instance types, and instance states.
Differentiating between On-Demand, Reserved, and Spot instances.

## Launching an EC2 Instance:

- Step-by-step guide on launching an EC2 instance using the AWS Management Console.
- Configuring instance details, such as instance type, network settings, and storage options.
- Understanding security groups and key pairs for securing instances.

## Managing EC2 Instances:

- Starting, stopping, and terminating instances.
- Monitoring instance performance and utilization.
- Basic troubleshooting and accessing instances using SSH (Secure Shell).
