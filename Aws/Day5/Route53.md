

## 🌐 **What is Amazon Route 53?**

**Route 53** is AWS's scalable and highly available **DNS (Domain Name System) web service** that:

* Routes traffic to websites and services
* Registers domain names
* Monitors health of endpoints
* Supports **global routing policies**

---

## 🧠 **Why It's Called Route 53?**

* 53 is the default port used for DNS services (TCP/UDP port 53)

---

## ✅ **Key Features of Route 53**

| Feature                 | Description                                                         |
| ----------------------- | ------------------------------------------------------------------- |
| **DNS Service**         | Resolve domain names to IP addresses (like www → 192.0.2.1)         |
| **Domain Registration** | Buy/manage domains directly from AWS                                |
| **Health Checks**       | Monitor endpoint health and route only to healthy resources         |
| **Routing Policies**    | Control traffic based on latency, geography, weight, failover, etc. |
| **Private DNS**         | Internal DNS within your VPCs for private hosted zones              |

---

## 📦 **Common Use Cases**

1. **Host a Website with Custom Domain**
   → e.g., map `www.mysite.com` to a CloudFront or S3 static site

2. **Failover Between Regions**
   → Route traffic to US if EU fails (failover policy)

3. **Latency-Based Routing**
   → Route users in Asia to servers in Singapore, US users to N. Virginia

4. **Route to ELB, S3, EC2, or Lambda**
   → Native integration with AWS resources via alias records

---

## ⚙️ **Step-by-Step Implementation: Hosting a Domain with Route 53**

### 🎯 Scenario:

You have a static website on **S3** or a web app on **ALB**, and you want to point `www.example.com` to it.

---

### ✅ **Step 1: Register a Domain (if needed)**

* Go to [Route 53 Console](https://console.aws.amazon.com/route53/)
* Click **“Registered Domains” > “Register Domain”**
* Choose a name (e.g., `mycoolproject.com`) and follow instructions

*OR* buy domain from external registrar (e.g., GoDaddy)

---

### ✅ **Step 2: Create a Hosted Zone**

* In Route 53 → **“Hosted Zones” > “Create Hosted Zone”**
* Type: **Public hosted zone**
* Name: `example.com`

This creates:

* **NS (Name Server) record** — used at domain registrar
* **SOA record** — start of authority

---

### ✅ **Step 3: Set Domain Nameservers (if external registrar)**

* Go to your domain registrar (e.g., GoDaddy)
* Update NS records to match the values shown in Route 53’s NS record

This connects your domain to AWS.

---

### ✅ **Step 4: Create Records**

You’ll now point your domain/subdomain to your resource (like an ALB or S3 bucket)

#### Option A: Point to S3 Website

* Create a new **A Record**
* Name: `www`
* Type: **A - IPv4 address**
* Alias: **Yes**
* Alias Target: Choose your **S3 static site endpoint**

#### Option B: Point to EC2 or Load Balancer

* Create **A Record**
* Name: `app` or `www`
* Type: **Alias**
* Target: Choose your **ALB** or **EC2 IP**

---

### ✅ **Step 5: Add HTTPS (Optional but Recommended)**

* Use **Amazon Certificate Manager (ACM)** to create free SSL cert
* Attach to CloudFront/ALB for HTTPS access

---

### ✅ **Step 6: Test the Setup**

1. Visit `www.yourdomain.com`
2. Use tools like [whatsmydns.net](https://www.whatsmydns.net/) to verify global DNS propagation

---

## 📊 **Routing Policies in Route 53 (with Examples)**

| **Policy Type**     | **Use Case**                                    |
| ------------------- | ----------------------------------------------- |
| Simple Routing      | Basic A or CNAME mapping                        |
| Weighted Routing    | Split traffic (e.g., 70% to v1, 30% to v2)      |
| Latency Routing     | Send users to closest region for performance    |
| Failover Routing    | Primary/Secondary setup for disaster recovery   |
| Geolocation Routing | Route based on user’s location (e.g., EU vs US) |
| Multivalue Answer   | Like Round Robin DNS with health checks         |

---

## 💡 Real-World Example

**Company**: Media site with users in US, India, Europe
**Need**: Low latency + global failover

* ✅ Use **Latency Routing** to direct traffic to nearest region
* ✅ Use **Failover Routing** to shift to backup region if health check fails
* ✅ Use **ACM** + CloudFront + Route 53 for secure, fast delivery


