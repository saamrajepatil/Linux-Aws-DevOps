

## 🛡️ What is AWS WAF?

**AWS WAF (Web Application Firewall)** is a **security service** that helps you **protect web applications** from:

* Common web exploits (SQL injection, XSS)
* Bot attacks
* DDoS attempts
* Malicious IPs or patterns

It's like a **shield in front of your web apps**, and it works with:

* **Amazon CloudFront (CDN)**
* **Application Load Balancer (ALB)**
* **API Gateway**
* **AWS AppSync**

---

## ✅ Key Features of AWS WAF

| Feature           | Description                                    |
| ----------------- | ---------------------------------------------- |
| **Rule-Based**    | Allow/block/monitor traffic using custom rules |
| **Managed Rules** | Pre-built rule sets by AWS or third parties    |
| **IP Sets**       | Block specific IPs or ranges                   |
| **Rate Limiting** | Throttle requests per IP                       |
| **Regex Match**   | Block requests with specific patterns          |
| **Logging**       | Logs to CloudWatch or S3 for visibility        |
| **Bot Control**   | Detect and mitigate bot traffic (paid feature) |

---

## 📚 Common Use Cases

| Use Case                   | Example                                           |
| -------------------------- | ------------------------------------------------- |
| SQL Injection              | Block requests with `" OR 1=1 --` in query string |
| Cross-site Scripting (XSS) | Block `<script>` tags in inputs                   |
| IP Blocking                | Block known bad IPs or countries                  |
| API Abuse Protection       | Limit request rate from one IP                    |
| OWASP Top 10 Protection    | Use managed rule groups for fast coverage         |
| Protect WordPress          | Block common WordPress attack patterns            |

---

## 🧩 How AWS WAF Works (Basic Flow)

```
Client → CloudFront / ALB / API Gateway
       → AWS WAF (checks rules)
       → Forwards request if allowed
```

---

## ⚙️ How to Implement AWS WAF (Step-by-Step)

### Step 1: Create a Web ACL

1. Go to AWS Console → **WAF & Shield**
2. Click **Create Web ACL**
3. Choose where to associate (CloudFront, ALB, API Gateway)
4. Add rules:

   * Managed AWS rules (e.g., AWSManagedRulesCommonRuleSet)
   * Custom rules (IP blocks, rate limits, geo match, etc.)

### Step 2: Add Rules

* You can **block, allow, or count** based on:

  * IP set
  * URI patterns
  * Header values
  * Request rate
  * Query string
  * Bot detection (optional)

### Step 3: Associate Web ACL with your resource

* Choose a resource like:

  * Application Load Balancer
  * API Gateway Stage
  * CloudFront Distribution

### Step 4: Save and Deploy

### Step 5: Monitor

* Use **CloudWatch logs** or **AWS WAF logging** (enable via S3 or Kinesis)

---

## 🔐 Example Rule: Block IP Range and SQL Injection

```yaml
RuleGroup:
  - Name: BlockBadIP
    Priority: 1
    Action: BLOCK
    Statement:
      IPSetReferenceStatement:
        Arn: arn:aws:wafv2:region:account-id:ipset/bad-ip-list

  - Name: SQLInjection
    Priority: 2
    Action: BLOCK
    Statement:
      SqliMatchStatement:
        FieldToMatch:
          Body: {}
        TextTransformations:
          - Priority: 0
            Type: NONE
```

---

## 🧠 Real-World Example

Imagine you have a public web app behind an **Application Load Balancer**. You can:

* Attach AWS WAF to ALB
* Use AWS managed rules to block XSS, SQLi
* Add IPSet to block malicious IPs
* Set rate limiting to 1000 reqs per 5 mins per IP
* Log traffic to CloudWatch

Done — you're protected with minimal setup.

---

## 📝 Pricing (Important!)

* Charged **per Web ACL** and **per rule**
* Plus **per request inspected**
* Can increase based on logging and bot control (optional)

---

## ✅ Summary

| Aspect        | Details                                         |
| ------------- | ----------------------------------------------- |
| What is it?   | Web Application Firewall for AWS                |
| Where to use? | ALB, CloudFront, API Gateway                    |
| Features      | SQLi/XSS protection, rate limiting, bot control |
| Setup Time    | \~10–15 minutes for basic protection            |
| Tools         | Console, Terraform, CloudFormation              |

