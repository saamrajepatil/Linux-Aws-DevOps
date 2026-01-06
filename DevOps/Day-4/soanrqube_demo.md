

---

# ✅ Best Way for SonarQube Demo on 1 Core CPU

### ✔️ Use **SonarQube Community Edition**

### ✔️ Use **single Docker container**

### ❌ Avoid Docker Compose (Postgres is heavy)

### ❌ Avoid SonarQube Enterprise

---

## 🖥 Minimum EC2 Recommendation

| Resource | Value                         |
| -------- | ----------------------------- |
| vCPU     | 1                             |
| RAM      | 4 GB (2 GB works but risky)   |
| Disk     | 20 GB                         |
| OS       | Amazon Linux 2 / Ubuntu 20.04 |

---

# 🔹 STEP 1: Install Docker

### Amazon Linux 2

```bash
sudo yum update -y
sudo yum install -y docker
sudo systemctl start docker
sudo usermod -aG docker ec2-user
newgrp docker
```

### Ubuntu

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo usermod -aG docker $USER
newgrp docker
```

---

# 🔹 STEP 2: Increase Kernel Limits (VERY IMPORTANT)

SonarQube **will NOT start** without this.

```bash
sudo sysctl -w vm.max_map_count=262144
sudo sysctl -w fs.file-max=65536
```

Make it permanent:

```bash
sudo nano /etc/sysctl.conf
```

Add:

```ini
vm.max_map_count=262144
fs.file-max=65536
```

Apply:

```bash
sudo sysctl -p
```

---

# 🔹 STEP 3: Run SonarQube (Lightweight Mode)

### Use **Community Edition**

```bash
docker run -d \
--name sonarqube \
-p 9000:9000 \
-e SONAR_ES_BOOTSTRAP_CHECKS_DISABLE=true \
sonarqube:9.9-community
```

✔️ `SONAR_ES_BOOTSTRAP_CHECKS_DISABLE=true`
→ **Critical for low-memory demo environments**

---

# 🔹 STEP 4: Verify Container

```bash
docker ps
docker logs sonarqube
```

Wait **2–3 minutes** ⏳

---

# 🔹 STEP 5: Access SonarQube UI

Open browser:

```
http://<EC2_PUBLIC_IP>:9000
```

### Default Login

```
Username: admin
Password: admin
```

(Change password on first login)

---

# 🔹 STEP 6: SonarQube Demo Flow (What to Teach)

### Demo Topics (15–30 mins)

1. **What is SonarQube**
2. Code Quality vs Security vs Bugs
3. Quality Gates
4. Rules & Profiles
5. Issues & Hotspots

---

# 🔹 STEP 7: Simple Java Demo Project

```bash
sudo yum install git maven -y   # Amazon Linux
```

```bash
git clone https://github.com/SonarSource/sonar-scanning-examples.git
cd sonar-scanning-examples/sonarqube-scanner-maven
```

---

# 🔹 STEP 8: Generate Token in SonarQube

* Login → **My Account → Security**
* Generate token
* Copy it

---

# 🔹 STEP 9: Run Maven Scan

```bash
mvn sonar:sonar \
  -Dsonar.projectKey=demo \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.login=<TOKEN>
```

✔️ Analysis will appear in SonarQube UI

---

# ⚠️ Important Notes for 1-Core Instances

| Item             | Advice                    |
| ---------------- | ------------------------- |
| DB               | Use embedded DB (default) |
| Concurrent scans | ❌ Avoid                   |
| Plugins          | ❌ Don’t install           |
| Docker Compose   | ❌ Heavy                   |
| JVM tuning       | Optional                  |

---

# 🔹 Optional: Reduce Memory Further (If Instance Crashes)

```bash
docker run -d \
--name sonarqube \
-p 9000:9000 \
-e SONAR_ES_BOOTSTRAP_CHECKS_DISABLE=true \
-e SONAR_WEB_JVM_OPTS="-Xmx512m -Xms256m" \
sonarqube:9.9-community
```

---

# 🎓 How to Explain to Students (Simple Line)

> “SonarQube analyzes source code to detect bugs, vulnerabilities, and code smells before code goes to production.”

---

## ✅ Summary (Best for Demo)

✔️ Docker (single container)
✔️ SonarQube Community
✔️ Works on **1 core + 4 GB RAM**
✔️ Easy to start / stop

---

If you want, I can also:

* Give **SonarQube + Jenkins demo pipeline**
* Share **common interview questions**
* Provide **slides structure** for teaching

Just tell me 👍
