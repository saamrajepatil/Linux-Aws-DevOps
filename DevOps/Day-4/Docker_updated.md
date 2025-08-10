
# 1. Quick Summary (What & Why)

**What is Docker?**
Docker is a containerization platform that packages an application and its dependencies into a lightweight, portable unit called a **container**. Containers share the host OS kernel but run in isolated user spaces.

**Why use Docker?**

* Consistent runtime across dev, test, prod ("works on my machine" solved)
* Faster startup than VMs, lower overhead
* Easier CI/CD, reproducible builds
* Microservices friendly

**Human analogy:**

* **Image = Recipe** (how to make the meal)
* **Container = Lunchbox** (the actual meal you take and eat)
* **Registry = Pantry/Store** (where you fetch recipes/images)
* **Dockerfile = Chef’s step-by-step recipe**
* **Volume = Fridge** (keeps persistent food between days)
* **Network = Roads** (how lunchboxes/containers communicate)

---

# 2. Learning Path (Zero → Hero)

1. Install Docker and run your first container.
2. Learn core commands to manage images & containers.
3. Write Dockerfile and build custom images.
4. Understand volumes, bind mounts, and data persistence.
5. Use networks for container communication.
6. Compose multiple services with `docker-compose`.
7. Optimize images (multi-stage builds, smaller base images).
8. Security basics and scanning images.
9. CI/CD integration and pushing to registry.
10. Intro to orchestration (Kubernetes/ECS) — high-level.

---

# 3. Install Docker (quick)

**Linux (Ubuntu example)**

```bash
# Update
sudo apt update
# Prereqs
sudo apt install apt-transport-https ca-certificates curl gnupg lsb-release -y
# Docker repo
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io -y
# Run without sudo (optional)
sudo usermod -aG docker $USER
# Log out & in again or newgrp docker
```

**macOS / Windows**

* Install **Docker Desktop** from docker.com and follow GUI steps.

Verify:

```bash
docker --version
docker run --rm hello-world
```

---

# 4. Core Concepts & Commands (Beginner)

**Run a container**

```bash
docker run -d --name mynginx -p 8080:80 nginx
```

* `-d` run in background (detached)
* `--name` friendly name
* `-p host:container` port mapping

**Key commands** (short list):

* `docker run`, `docker ps`, `docker ps -a`, `docker stop`, `docker rm`
* `docker images`, `docker rmi`, `docker pull`, `docker push`
* `docker logs <container>`, `docker exec -it <container> /bin/bash`
* `docker build -t myimage:tag .`

**Inspect**

```bash
docker inspect mynginx
```

**Remove stopped containers & dangling images**

```bash
docker container prune
docker image prune -a
```

**Analogy:** Running a container is like ordering a meal from a recipe: the kitchen prepares a lunchbox (container) from a recipe (image).

---

# 5. Dockerfile — Write a Recipe (Intermediate)

**A minimal Dockerfile for a Node.js app**

```Dockerfile
# Use official Node base image
FROM node:18-alpine

# Set working directory
WORKDIR /usr/src/app

# Copy package files & install deps (leverage cache)
COPY package*.json ./
RUN npm ci --only=production

# Copy app source
COPY . .

# Expose port
EXPOSE 3000

# Run the app
CMD ["node", "server.js"]
```

**Build & run**

```bash
docker build -t mynodeapp:1.0 .
docker run -d -p 3000:3000 mynodeapp:1.0
```

**Multi-stage build (to reduce size)**

```Dockerfile
# build stage
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# runtime stage
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
```

**Analogy:** Multi-stage builds are like cooking in a prep kitchen, then packaging only the final meal — you discard the messy prep area and keep only the finished lunchbox.

---

# 6. Volumes and Persistence

**Bind mount (host dir ↔ container)**

```bash
docker run -v /host/path:/container/path -d myapp
```

**Named volume**

```bash
docker volume create mydata
docker run -v mydata:/data -d mydb
```

**When to use what?**

* Use bind mounts for local development (live code changes).
* Use named volumes for managed persistent data (databases).

**Analogy:** Volume = fridge. If you throw leftovers in fridge (volume), they persist across days (container restarts). If you store them on a guest's table (container ephemeral disk), they vanish when the guest leaves.

---

# 7. Networking (Container Communication)

**Default bridge network** (containers can reach each other by IP on the bridge). Not great for multi-host.

**User-defined bridge network** (gives DNS service discovery by container name):

```bash
docker network create mynet
docker run -d --network mynet --name app1 myapp
docker run -d --network mynet --name app2 myother
# app1 can reach app2 at hostname 'app2'
```

**Host network** (use host's network stack): `--network host` (Linux only)

**Analogy:** Networks are like roads connecting different restaurants — user-defined networks add signposts (DNS) so you can call restaurants by name.

---

# 8. docker-compose — Multi-container apps (Hands-on)

**docker-compose.yml** example (web + redis)

```yaml
version: '3.8'
services:
  web:
    build: ./web
    ports:
      - "8000:8000"
    depends_on:
      - redis
  redis:
    image: redis:6-alpine
    volumes:
      - redisdata:/data

volumes:
  redisdata:
```

**Commands**

```bash
docker-compose up --build -d
docker-compose logs -f
docker-compose down
```

**Analogy:** Compose is like planning a multi-course meal — you define all dishes (services) and how they share ingredients (volumes/networks).

---

# 9. Image Registries (Push & Pull)

**Docker Hub / private registry / ECR / GCR**

**Tag & push example (Docker Hub)**

```bash
docker tag myapp:1.0 username/myapp:1.0
docker push username/myapp:1.0
```

**AWS ECR example**

```bash
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.us-east-1.amazonaws.com
docker tag myapp:1.0 <aws_account_id>.dkr.ecr.us-east-1.amazonaws.com/myapp:1.0
docker push <aws_account_id>.dkr.ecr.us-east-1.amazonaws.com/myapp:1.0
```

**Analogy:** Push to registry = stock the pantry with your recipes (images) so other kitchens (servers) can fetch and cook them.

---

# 10. Image Optimization & Best Practices

* Use small base images (`alpine`, `distroless`) when possible
* Use multi-stage builds to avoid dev tools in final image
* Minimize layers (combine `RUN` where sensible)
* Take advantage of build cache by copying `package.json` first and running dependency install before copying source
* Scanning images: `trivy image myimage:tag` or other scanners

---

# 11. Docker Security Basics

* Do not run containers as root. Use `USER` in Dockerfile.
* Use official and scanned base images.
* Keep secrets out of images — use environment variables or secret stores (Docker secrets, Kubernetes secrets, Vault).
* Limit container capabilities and use seccomp/AppArmor profiles.

**Analogy:** Security is like food safety — don't reuse contaminated utensils (secrets), and lock the fridge (restrict access).

---

# 12. Debugging & Troubleshooting

* `docker logs <container>` — check container stdout/stderr
* `docker exec -it <container> sh` — inspect file system/runtime
* `docker inspect` — see low-level metadata
* Check exit code: `docker ps -a` shows containers with status
* Common issue: container starts and exits (check CMD/CMD args, missing files or env vars)

---

# 13. CI/CD Integration (Example with Jenkins)

**Simple Jenkins pipeline snippet**

```groovy
pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t myapp:${BUILD_ID} .'
      }
    }
    stage('Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          sh 'docker login -u $USER -p $PASS'
          sh 'docker tag myapp:${BUILD_ID} username/myapp:${BUILD_ID}'
          sh 'docker push username/myapp:${BUILD_ID}'
        }
      }
    }
    stage('Deploy') {
      steps {
        sh 'ssh ec2-user@host "docker pull username/myapp:${BUILD_ID} && docker stop myapp || true && docker rm myapp || true && docker run -d --name myapp -p 80:3000 username/myapp:${BUILD_ID}"'
      }
    }
  }
}
```

---

# 14. Orchestration (Short Overview)

* **Docker Swarm**: built-in simple orchestration (deprecated in many orgs).
* **Kubernetes**: industry standard; manages scaling, service discovery, rolling updates, stateful sets.
* **ECS/EKS**: AWS managed container platforms.

**When to learn next:** After you are comfortable with Docker workflows and Compose, learn Kubernetes basics.

---

# 15. Exercises (Hands-on)

**Beginner exercises**

1. Install Docker and run `hello-world`.
2. Pull `nginx` and serve a simple static HTML (use volume mount).
3. Build a Dockerfile for a simple Python Flask app and run it.

**Intermediate**

1. Create a `docker-compose` app with web + database + redis.
2. Convert a multi-stage build from a simple Node app.
3. Configure a named volume to persist DB data and verify persistence.

**Advanced**

1. Scan images with `trivy` and fix vulnerabilities.
2. Create a CI pipeline to build/push images and deploy to a test server.
3. Containerize a legacy app and measure performance differences vs VM.

---

# 16. Common Interview Questions & Short Answers

* **Why use containers vs VMs?** Containers are lightweight, faster startup, share kernel, and better density. VMs are fully isolated (hypervisor), suited for different OS per VM.
* **What is a Docker image?** Immutable snapshot built from Dockerfile. A container is a running instance of an image.
* **How do you persist data?** Use volumes (named volumes or bind mounts).
* **How do you optimize image size?** Multi-stage builds and smaller base images.

---

# 17. Cheat Sheet — Most Used Commands

```text
docker run -d -p HOST:CONTAINER --name name image
docker ps
docker ps -a
docker stop CONTAINER
docker rm CONTAINER
docker logs CONTAINER
docker exec -it CONTAINER /bin/sh
docker build -t name:tag .
docker images
docker rmi image
docker pull image
docker push image
docker-compose up --build -d
docker-compose down
docker volume create name
docker network create name
docker inspect CONTAINER
```

---

# 18. Resources & Next Steps

* Official Docker docs: [https://docs.docker.com/](https://docs.docker.com/)
* Play with Docker: [https://labs.play-with-docker.com/](https://labs.play-with-docker.com/)
* Traefik/Nginx examples for reverse proxy
* Kubernetes basics after mastering Docker

---

# 19. Teaching Tips (for instructors)

* Start with analogies and one-liners before commands.
* Use live demo (install & run) — students remember seeing it work.
* Give small exercises that build on each other (run → build → compose).
* Encourage exploring `docker inspect` and reading logs.

---

# 20. Appendix — Sample Minimal Web App (Node) Files

**server.js**

```js
const http = require('http');
const port = process.env.PORT || 3000;
const server = http.createServer((req, res) => {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello from Docker!');
});
server.listen(port, () => console.log('Server running on', port));
```

**package.json**

```json
{ "name": "simpleapp", "version": "1.0.0", "scripts": { "start": "node server.js" } }
```

**Dockerfile** (multi-stage not required for this)

```Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
EXPOSE 3000
CMD ["node","server.js"]
```

Build & run

```bash
docker build -t simpleapp:1.0 .
docker run -d -p 3000:3000 simpleapp:1.0
curl http://localhost:3000
```

---
