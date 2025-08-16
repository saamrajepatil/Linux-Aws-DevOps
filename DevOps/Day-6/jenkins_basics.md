
# 📘 Jenkins Overview – Student Guide

## 🔹 What is Jenkins?

* Jenkins is an **open-source automation server** written in Java.
* It helps automate parts of the **software development lifecycle (SDLC)**:

  * **Building** code
  * **Testing** applications
  * **Deploying** to environments
* Supports **CI/CD (Continuous Integration / Continuous Delivery)**.
* Extensible with **plugins** (1,800+ available).

---

## 🔹 Jenkins Architecture

1. **Master (Controller):**

   * Central server managing Jenkins configuration.
   * Schedules jobs, monitors nodes, provides UI, and distributes builds.

2. **Slave (Agent):**

   * Worker machine that executes build tasks.
   * Can run on Linux/Windows, Docker containers, or Kubernetes pods.

3. **Communication:**

   * Master → delegates job execution to slave nodes via **SSH** or **JNLP**.
   * Slave → sends results back to master.

👉 This is called **Master-Slave (Master-Agent) architecture**.

---


## Installation on Amazon linux 2023
Step 2: Update the System
It’s always a good practice to ensure that your system is up to date. Open a terminal window and enter the following command:

sudo dnf update

Step 3: Install Java

Jenkins is a Java application, so Java is a prerequisite. Install Java with the following command:

sudo yum install java-17-amazon-corretto-devel

Verify the installation with:

java -version

Step 4: Add the Jenkins Repository

Next, add the Jenkins repository to your system with the following commands:

sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo

Import a key file from Jenkins-CI to enable installation from the package:

sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

Step 5: Install Jenkins
Now, install Jenkins by running:

sudo dnf install jenkins -y

Step 6: Start and Enable Jenkins

Enable and start the Jenkins service using:

sudo systemctl enable jenkins

sudo systemctl start jenkins

Step 7: Access Jenkins
Now, open your web browser and access Jenkins by navigating to:

http://your_amazon_linux_instance_ip:8080

You will see a setup wizard and be prompted to enter the administrator password.

OR

## 🔹 Installation on Ubuntu Linux

1. Update system packages:

   ```bash
   sudo apt update
   sudo apt upgrade -y
   ```

2. Install Java (Jenkins requires Java):

   ```bash
   sudo apt install openjdk-11-jdk -y
   ```

3. Add Jenkins repository & key:

   ```bash
   curl -fsSL https://pkg.jenkins.io/debian/jenkins.io.key | sudo tee \
     /usr/share/keyrings/jenkins-keyring.asc > /dev/null
   echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
     https://pkg.jenkins.io/debian binary/ | sudo tee \
     /etc/apt/sources.list.d/jenkins.list > /dev/null
   ```

4. Install Jenkins:

   ```bash
   sudo apt update
   sudo apt install jenkins -y
   ```

5. Start and enable service:

   ```bash
   sudo systemctl start jenkins
   sudo systemctl enable jenkins
   ```

6. Access Jenkins in browser:

   ```
   http://<server-ip>:8080
   ```

7. Get initial admin password:

   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

---

## 🔹 Master-Slave (Agent) Setup

* **Why?** → To distribute workloads across multiple nodes.
* **Steps to add a slave node:**

  1. Go to **Jenkins Dashboard → Manage Jenkins → Nodes → New Node**.
  2. Give name & type (permanent agent).
  3. Configure:

     * Remote root directory
     * Labels (to target specific jobs)
     * Launch method (via SSH or JNLP).
  4. Start agent on node:

     * Jenkins provides an `agent.jar` file to run on the slave machine.

---

## 🔹 Jenkins Components

* **Job/Project** – Unit of work (e.g., build/test/deploy).
* **Build Executor** – Worker threads on nodes that run jobs.
* **Plugins** – Extend Jenkins (Git, Docker, Kubernetes, Slack, etc.).
* **Pipeline** – Scripted or declarative workflow to define CI/CD steps.
* **Views** – Dashboard for monitoring jobs.
* **Credentials** – Secure storage for passwords, SSH keys, tokens.
* **Artifacts** – Output files from builds.

---

## 🔹 Why Jenkins is Secure?

* **Role-based access control (RBAC)** – restricts who can access/manage jobs.
* **Credentials plugin** – securely stores secrets.
* **HTTPS & LDAP integration** – for enterprise-grade authentication.
* **Agent-to-master security** – only approved nodes can connect.

---

## 🔹 Types of Builds

1. **Freestyle Project** – Simple GUI-based configuration.
2. **Maven Project** – For Java-based projects using Maven.
3. **Pipeline (Declarative/Scripted)** – Advanced CI/CD automation via code.
4. **Multibranch Pipeline** – Automatically creates jobs for branches.
5. **GitHub/Bitbucket Integration** – Triggers builds on push/PR.

---

## 🔹 Jenkins Pipeline

* Pipelines are defined in a **Jenkinsfile** (Groovy syntax).
* Example Declarative Pipeline:

  ```groovy
  pipeline {
      agent any
      stages {
          stage('Build') {
              steps {
                  echo 'Building...'
              }
          }
          stage('Test') {
              steps {
                  echo 'Testing...'
              }
          }
          stage('Deploy') {
              steps {
                  echo 'Deploying...'
              }
          }
      }
  }
  ```

---

## 🔹 Other Important Notes 

* Jenkins runs on port **8080** by default.
* Commonly integrated with **Git, Docker, Kubernetes, Ansible**.
* Backup important folders: `/var/lib/jenkins/`.
* **Blue Ocean plugin** → modern UI for pipelines.
* Jenkins supports **distributed builds** → helpful for large teams.
