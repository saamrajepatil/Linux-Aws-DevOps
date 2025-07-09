

### 🔹 VPC Basics

**1. What is a VPC?**
A Virtual Private Cloud is an isolated virtual network within AWS where you define IP ranges, subnets, gateways, and security.

**2. Why use a VPC?**
For logical isolation, network control, security zones, and to run AWS resources in a defined network environment.

---

### 🔹 Subnets & IP Addressing

**3. What is a subnet?**
A range of IP addresses within a VPC defined by a CIDR block, often designated as public or private.

**4. Difference between public and private subnets?**
Public subnets have a route to an Internet Gateway (IGW), while private subnets do not and typically use NAT for outbound Internet.

**5. What is CIDR notation?**
Classless Inter-Domain Routing, e.g., 10.0.0.0/16 defines a block of 65,536 IPs.

---

### 🔹 Routing Components

**6. What is an Internet Gateway (IGW)?**
A horizontally scaled, redundantly deployed VPC component that enables communication between VPC and the Internet.

**7. What is a NAT Gateway?**
Managed service that enables instances in a private subnet to access the Internet outbound, while preventing inbound traffic.

**8. What is a Route Table?**
Defines how traffic is routed from subnets to destinations like IGWs, NAT gateways, other subnets, or peered VPCs.

---

### 🔹 Network Security

**9. What is a Security Group?**
Stateful virtual firewall at the instance level that controls inbound and outbound traffic.

**10. What is a Network ACL (NACL)?**
Stateless subnet-level firewall that allows and denies traffic. You must define both directions explicitly.

**11. SG vs NACL differences?**
SGs are stateful and applied at instance level; NACLs are stateless and applied per subnet.

---

### 🔹 Connectivity

**12. What is VPC Peering?**
Direct, private networking between two VPCs enabling data communication within AWS without transit.

**13. What is AWS Transit Gateway?**
Scalable hub-and-spoke model connecting multiple VPCs and on-premises networks via a central gateway.

**14. When to use Transit Gateway vs Peering?**
Use peering for simple, small-scale connections. Use Transit Gateway for many VPCs or complex, scalable routing.

---

### 🔹 IP Management

**15. What is an Elastic IP?**
Static public IPv4 address which you can assign/disassociate to/from an instance or NAT gateway.

**16. What is secondary private IP?**
Additional private IP addresses assigned to an instance or Elastic Network Interface (ENI), used for Docker, aliases, etc.

---

### 🔹 High Availability

**17. What is AZ, and why create subnets per AZ?**
An Availability Zone is an isolated data center. Subnets across AZs ensure fault tolerance and high availability.

**18. How does cross-zone load balancing work?**
Using an ALB/NLB, traffic is distributed evenly across healthy targets in different AZs.

---

### 🔹 Hybrid / On-Prem

**19. What is a VPN connecting on-prem to VPC?**
Creates an IPsec-encrypted tunnel between on-prem equipment and a virtual private gateway in your VPC.

**20. What is AWS Direct Connect?**
A dedicated network connection between on-prem data center and AWS, providing higher bandwidth and lower latency.

---

### 🔹 Routing & NAT

**21. How do you enable NAT for private subnets?**
Include a route in private subnet route table pointing `0.0.0.0/0` to NAT gateway/instance in a public subnet.

**22. Can a NAT instance be in a private subnet?**
No — NAT instance must reside in a public subnet with a route to IGW.

---

### 🔹 Security & Monitoring

**23. What are VPC Flow Logs?**
Capture metadata for IP traffic in/out of network interfaces or subnets for analysis or auditing.

**24. Where can you publish flow logs?**
To CloudWatch Logs or S3 buckets.

---

### 🔹 Advanced Components

**25. What is an Elastic Network Interface (ENI)?**
A virtual network card you can attach to instances with multiple IPs or security groups.

**26. What is VPC endpoint?**
A private link to AWS services (like S3, DynamoDB) without using the Internet via interface or gateway endpoints.

---

### 🔹 Use Cases & Limitations

**27. Can two subnets overlap?**
No—IP ranges within one VPC cannot overlap. In peered VPCs, overlapping subnets block peer creation.

**28. How to host a public web service?**
Place instances in a public subnet with IGW and security groups allowing inbound 80/443.

**29. How to host a backend API privately?**
Put API servers in a private subnet, use ALB in public subnet, route traffic to private subnet targets, and isolate DBs.

---

### 🔹 Hands-on Testing

**30. How do you troubleshoot connectivity issues?**
Check subnet route tables, IGW/NAT presence, Security Group and NACL rules, ensure ENI assigned correctly, and view flow logs.
