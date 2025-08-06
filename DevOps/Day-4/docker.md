

 🔹 What is Docker?

Docker is an **open-source containerization platform** that allows developers to package applications and all their dependencies into **containers**.

* **Container**: A lightweight, standalone, and executable unit that includes code, runtime, libraries, and system tools.
* Think of it as a **lightweight VM** but sharing the host OS kernel.

---

 🔹 Why Docker?

* "Works on my machine" problem solved.
* Faster deployments.
* Better resource utilization.
* Easy testing and rollback.

---

 🔹 How Docker Evolved

| Era              | Technology                    | Limitation Solved by Docker             |
| ---------------- | ----------------------------- | --------------------------------------- |
| Physical Server  | Heavy & hard to scale         | Docker introduced containers            |
| Virtual Machines | Heavy, large image, slow boot | Docker containers are lightweight, fast |
| Containers       | Lightweight, consistent       | Better CI/CD, DevOps adoption           |

---

 🔹 Docker vs Virtual Machine vs EC2

| Feature        | Docker Container     | Virtual Machine (VM)    | EC2 Instance          |
| -------------- | -------------------- | ----------------------- | --------------------- |
| OS Boot Time   | Seconds              | Minutes                 | Minutes               |
| Resource Usage | Low                  | High                    | High                  |
| Portability    | High                 | Medium                  | Medium                |
| Use case       | Microservices, CI/CD | Monolithic apps, legacy | Scalable infra on AWS |
| Shared Kernel  | Yes                  | No                      | No                    |

---

 🔹 What is a Dockerfile?

A **Dockerfile** is a text file containing instructions to build a Docker image.

# 🔸 Example Dockerfile:

```Dockerfile
# Use official Python image as base
FROM python:3.9-slim

# Set work directory
WORKDIR /app

# Copy local files to container
COPY . .

# Install dependencies
RUN pip install -r requirements.txt

# Command to run the app
CMD ["python", "app.py"]
```

---

 🔹 Build & Run a Docker Image

```bash
docker build -t my-python-app .
docker run -d -p 5000:5000 my-python-app
```

---

 🔹 20 Common Docker Commands with Explanation

| Command                            | Description                              |
| ---------------------------------- | ---------------------------------------- |
| `docker --version`                 | Check installed Docker version           |
| `docker info`                      | Show Docker system-wide info             |
| `docker pull nginx`                | Download image from DockerHub            |
| `docker images`                    | List all downloaded images               |
| `docker run hello-world`           | Run a test container                     |
| `docker ps`                        | Show running containers                  |
| `docker ps -a`                     | Show all containers (running + stopped)  |
| `docker stop <container>`          | Stop a running container                 |
| `docker start <container>`         | Start an existing container              |
| `docker restart <container>`       | Restart a container                      |
| `docker rm <container>`            | Remove container                         |
| `docker rmi <image>`               | Remove image                             |
| `docker build -t myapp .`          | Build image from Dockerfile              |
| `docker run -it ubuntu`            | Run an interactive container             |
| `docker exec -it <container> bash` | Access terminal inside running container |
| `docker logs <container>`          | View logs of container                   |
| `docker network ls`                | List all Docker networks                 |
| `docker volume ls`                 | List Docker volumes                      |
| `docker-compose up`                | Start services using docker-compose.yml  |
| `docker-compose down`              | Stop services defined in docker-compose  |

---

 🔹 How to Install Docker (Linux/Ubuntu)

```bash
# 1. Update packages
sudo apt update

# 2. Install prerequisites
sudo apt install apt-transport-https ca-certificates curl gnupg lsb-release -y

# 3. Add Docker’s official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# 4. Set up repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] \
https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 5. Install Docker Engine
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io -y

# 6. Start Docker
sudo systemctl start docker
sudo systemctl enable docker

# 7. Check installation
docker --version
```

---

 🔹 Benefits of Docker

| Benefit         | Description             |
| --------------- | ----------------------- |
| Portability     | Run anywhere            |
| Speed           | Faster than VMs         |
| Isolation       | Apps don't conflict     |
| Scalability     | Easily scale containers |
| CI/CD           | Perfect for pipelines   |
| Version control | Images are versioned    |

---

 🔹 Real-World Use Case: Flask App with MySQL (2-Tier Architecture)

# Architecture:

```
Browser → Flask App (Container) → MySQL DB (Container)
```

# Step-by-Step:

**1. Flask App (`app.py`):**

```python
from flask import Flask
import mysql.connector

app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello from Flask with MySQL!'

if __name__ == '__main__':
    app.run(host='0.0.0.0')
```

**2. Dockerfile for Flask App:**

```Dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install flask mysql-connector-python
CMD ["python", "app.py"]
```

**3. `docker-compose.yml`:**

```yaml
version: '3'
services:
  web:
    build: .
    ports:
      - "5000:5000"
    depends_on:
      - db
  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: testdb
```

**4. Run Application:**

```bash
docker-compose up --build
```

---

 🔹 Industry Use Cases of Docker

| Industry   | Use Case                           |
| ---------- | ---------------------------------- |
| DevOps     | CI/CD pipelines                    |
| QA         | Test environments                  |
| SaaS       | Microservices deployment           |
| Banking    | Isolated container apps            |
| Education  | Hands-on labs for students         |
| ML/AI      | Isolated Python environments       |
| E-commerce | Scaling product services           |
| Media      | Transcoding services in containers |

---


