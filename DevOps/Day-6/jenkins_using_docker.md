

## **Step 1: Install Docker**

Make sure Docker is installed on your system. Check with:

```bash
docker --version
```

If not installed:

* **Ubuntu/Debian**

  ```bash
  sudo apt update
  sudo apt install docker.io -y
  sudo systemctl start docker
  sudo systemctl enable docker
  ```
* **Amazon Linux / CentOS / RHEL**

  ```bash
  sudo yum install docker -y
  sudo systemctl start docker
  sudo systemctl enable docker
  ```
* Add your user to the `docker` group (optional to avoid sudo):

  ```bash
  sudo usermod -aG docker $USER
  ```

---

## **Step 2: Pull Jenkins Docker Image**

We’ll use the **official Jenkins LTS image**:

```bash
docker pull jenkins/jenkins:lts
```

---

## **Step 3: Run Jenkins Container**

Run Jenkins on port **8080**:

```bash
docker run -d \
  --name jenkins \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
```

* `-p 8080:8080` → Jenkins UI (browser access)
* `-p 50000:50000` → For Jenkins agents (slave nodes)
* `-v jenkins_home:/var/jenkins_home` → Persistent storage for Jenkins configs

---

## **Step 4: Get Initial Admin Password**

When Jenkins starts, it generates a password. Run:

```bash
docker logs jenkins
```

Look for something like:

```
*************************************************************
Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

abcd1234efgh5678ijklmnopqrstuvwx
*************************************************************
```

---

## **Step 5: Access Jenkins in Browser**

Open:

```
http://<your-server-ip>:8080
```

* Enter the **admin password** from above.
* Install **Suggested Plugins**.
* Create your **Admin User**.

---

## **Step 6: Verify & Use Jenkins**

You can now create jobs, pipelines, and start using Jenkins.

