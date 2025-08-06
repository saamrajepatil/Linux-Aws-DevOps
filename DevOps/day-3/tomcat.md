
🧠 What is Apache Tomcat?

**Apache Tomcat** is an open-source Java Servlet container developed by the Apache Software Foundation. It is used to:

* Run **Java-based web applications**
* Implement **Java Servlet**, **JSP (Java Server Pages)**, and **WebSocket**
* Host WAR (Web Application Archive) files

Tomcat is lightweight, widely used, and works well for **Java Web Projects**, especially in development and staging environments.

---

✅ Prerequisites

1. **Amazon EC2 instance**

   * OS: Amazon Linux 2 or Ubuntu
   * Open ports: 22 (SSH), 8080 (Tomcat default), 80 (optional for redirection)
2. **Java JDK installed**
3. **Apache Tomcat downloaded and configured**
4. **A sample `.war` file** to deploy (You can create a simple Java project using Maven or get a ready WAR file)

---

🚀 Step-by-Step: Install Tomcat on EC2 and Deploy WAR

---

#🔹 Step 1: Launch EC2 Instance

1. Go to AWS Console → EC2 → Launch Instance
2. Select Amazon Linux 2 AMI (or Ubuntu)
3. Choose t2.micro (free tier)
4. Configure Security Group:

   * Inbound Rules: Add **SSH (22)** and **Custom TCP (8080)**
5. Launch instance and connect using SSH.

---

#🔹 Step 2: Install Java

##For Amazon Linux 2:

```bash
sudo yum update -y
sudo amazon-linux-extras enable corretto8
sudo yum install java-1.8.0-amazon-corretto -y
java -version
```

##For Ubuntu:

```bash
sudo apt update
sudo apt install default-jdk -y
java -version
```

---

#🔹 Step 3: Download and Install Tomcat

```bash
cd /opt
sudo curl -O https://downloads.apache.org/tomcat/tomcat-9/v9.0.88/bin/apache-tomcat-9.0.88.tar.gz
sudo tar -xzf apache-tomcat-9.0.88.tar.gz
sudo mv apache-tomcat-9.0.88 tomcat9
sudo chmod -R 755 tomcat9
```

---

#🔹 Step 4: Start Tomcat

```bash
cd /opt/tomcat9/bin
sudo ./startup.sh
```

* Tomcat should now be running at:
  **http\://<your-ec2-public-ip>:8080**

---

#🔹 Step 5: Allow Port 8080 in EC2 Security Group

* Go to EC2 Dashboard → Security Groups → Inbound rules → Add Rule

  * Type: Custom TCP
  * Port Range: **8080**
  * Source: Anywhere (or custom IP)

---

#🔹 Step 6: Deploy WAR File

1. Upload your `.war` file to EC2:

```bash
scp -i your-key.pem yourapp.war ec2-user@<public-ip>:/tmp
```

2. Move WAR file to Tomcat `webapps`:

```bash
sudo mv /tmp/yourapp.war /opt/tomcat9/webapps/
```

3. Tomcat will automatically extract and deploy it in a few seconds.

4. Access it:

```
http://<your-ec2-public-ip>:8080/yourapp
```

---

🧰 Use Case Example

#Use Case: Hosting a Spring Boot or Java EE App for Dev/Test

* Teams deploy backend Java applications (.war) to Tomcat on EC2 for:

  * Development testing
  * Internal tools
  * Lightweight microservice hosting (for simple apps)

Example:

```bash
sudo mv my-springapp.war /opt/tomcat9/webapps/
```

---

🧩 Optional: Make Tomcat a Service (autostart)

Create a systemd service file:

```bash
sudo nano /etc/systemd/system/tomcat.service
```

Paste the following (modify paths if needed):

```ini
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking
User=ec2-user
Group=ec2-user
ExecStart=/opt/tomcat9/bin/startup.sh
ExecStop=/opt/tomcat9/bin/shutdown.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Then:

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable tomcat
sudo systemctl start tomcat
```

---

📘 Summary

| Component    | Description                           |
| ------------ | ------------------------------------- |
| **Tomcat**   | Java web server to deploy WAR files   |
| **WAR File** | Web app archive built from Java app   |
| **EC2**      | Host the Tomcat server                |
| **Ports**    | 8080 (Tomcat), 22 (SSH)               |
| **Benefits** | Lightweight, easy to deploy Java apps |

---
sample project: 
---

## 🔧 **1. Prerequisites**

Before you begin, ensure the following:

* ✅ AWS account
* ✅ One Amazon EC2 instance running **Amazon Linux 2**
* ✅ SSH access to the EC2 instance
* ✅ Inbound Security Group rule for port **8080** (for Tomcat)

---

## 🧱 **2. Launch EC2 Instance (Amazon Linux 2)**

1. Go to the **AWS Console > EC2 > Launch Instance**
2. Choose **Amazon Linux 2 AMI**
3. Instance type: `t2.micro` (Free tier)
4. Configure security group to allow:

   * SSH (port 22)
   * HTTP (port 80)
   * **Custom TCP rule (port 8080)** – required for Tomcat
5. Launch and connect via SSH.

---

## 🔐 **3. Connect to EC2 via SSH**

```bash
ssh -i your-key.pem ec2-user@<EC2_PUBLIC_IP>
```

---

## 📦 **4. Install Java (OpenJDK 11)**

Tomcat requires Java to run.

```bash
sudo yum update -y
sudo amazon-linux-extras enable corretto11
sudo yum install java-11-amazon-corretto -y
java -version
```

---

## 🐱 **5. Install Apache Tomcat**

### ✅ Step-by-step:

1. **Download Tomcat:**

```bash
cd /opt
sudo curl -O https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.85/bin/apache-tomcat-9.0.85.tar.gz
```

2. **Extract Tomcat:**

```bash
sudo tar -xvzf apache-tomcat-9.0.85.tar.gz
sudo mv apache-tomcat-9.0.85 tomcat9
```

3. **Set Permissions:**

```bash
sudo chmod -R 755 /opt/tomcat9
sudo chown -R ec2-user:ec2-user /opt/tomcat9
```

4. **Start Tomcat:**

```bash
cd /opt/tomcat9/bin
./startup.sh
```

5. **Check if Tomcat is running:**

Open browser:

```
http://<EC2_PUBLIC_IP>:8080
```

You should see the **Apache Tomcat homepage**.

---

## 🛠️ **6. Deploy a Sample Java `.war` Application**

Tomcat auto-deploys `.war` files placed in the `webapps` folder.

### ✅ Sample WAR File (Spring Boot Hello World):

```bash
cd /opt/tomcat9/webapps
curl -O https://tomcat.apache.org/tomcat-7.0-doc/appdev/sample/sample.war
```

Tomcat will auto-extract the `sample.war` and deploy it.

📍Access the app in browser:

```
http://<EC2_PUBLIC_IP>:8080/sample
```

---

## 🚀 **7. Setup Tomcat as a Systemd Service (Optional but Recommended)**

To auto-start Tomcat on reboot:

1. Create the service file:

```bash
sudo nano /etc/systemd/system/tomcat.service
```

Paste below:

```ini
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking

User=ec2-user
Group=ec2-user

Environment=JAVA_HOME=/usr/lib/jvm/java-11-amazon-corretto
Environment=CATALINA_PID=/opt/tomcat9/temp/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat9
Environment=CATALINA_BASE=/opt/tomcat9
ExecStart=/opt/tomcat9/bin/startup.sh
ExecStop=/opt/tomcat9/bin/shutdown.sh

[Install]
WantedBy=multi-user.target
```

2. Reload systemd and enable service:

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable tomcat
sudo systemctl start tomcat
```

Check status:

```bash
sudo systemctl status tomcat
```

---

## 🎯 Recap

| Step           | Description                              |
| -------------- | ---------------------------------------- |
| EC2 Setup      | Launched Amazon Linux 2 instance         |
| Java Install   | Installed OpenJDK 11                     |
| Tomcat Install | Downloaded & started Apache Tomcat 9     |
| App Deployment | Deployed sample.war to `webapps`         |
| Port 8080      | Opened in Security Group                 |
| Service Setup  | (Optional) Made Tomcat a systemd service |

---

## 🧪 Industry Use Case: Dev/Test Deployment

Many teams use Tomcat on EC2 for:

* CI/CD testing environments
* Hosting internal Java web apps
* Load testing WAR deployments
* Showcasing POCs

---

