
### 1. **Introduction to Docker**  
   - **What is Docker?** Docker is an open-source platform used to automate the deployment, scaling, and management of applications in lightweight containers.
   - **Benefits of Docker**: Portability, efficiency, consistency across environments, and improved CI/CD processes.

### 2. **Docker Installation**
   - **Linux**: Install Docker with commands:
     ```sh
     sudo apt-get update
     sudo apt-get install docker-ce docker-ce-cli containerd.io
     ```
   - **Windows and Mac**: Install Docker Desktop.

### 3. **Docker Components**
   - **Docker Engine**: The runtime to manage containers.
   - **Docker Images**: Read-only templates to create containers.
   - **Docker Containers**: Running instances of Docker images.
   - **Docker Hub**: A registry to store Docker images.

### 4. **Basic Docker Commands**
   - **Check Docker version**: `docker --version`
   - **Pull an image**: `docker pull ubuntu`
   - **List Docker images**: `docker images`
   - **Run a container**:
     ```sh
     docker run -it --name mycontainer ubuntu
     ```
     This runs an interactive terminal (`-it`) in the `ubuntu` container.
   - **List running containers**: `docker ps`
   - **Stop a container**: `docker stop mycontainer`
   - **Remove a container**: `docker rm mycontainer`

### 5. **Dockerfile**
   - **What is Dockerfile?**: A script used to build Docker images automatically by specifying a set of instructions.
   - **Example Dockerfile**:
     ```dockerfile
     FROM ubuntu:latest
     RUN apt-get update && apt-get install -y nginx
     CMD ["nginx", "-g", "daemon off;"]
     ```
   - **Build an image from Dockerfile**:
     ```sh
     docker build -t mynginx .
     ```

### 6. **Docker Compose**
   - **What is Docker Compose?**: A tool to define and run multi-container Docker applications.
   - **Example `docker-compose.yml`**:
     ```yaml
     version: '3'
     services:
       web:
         image: nginx
         ports:
           - "8080:80"
     ```
   - **Run with Docker Compose**: `docker-compose up`

### 7. **Networking in Docker**
   - **Bridge network**: Default network driver.
   - **Create a network**:
     ```sh
     docker network create mynetwork
     ```
   - **Attach container to a network**:
     ```sh
     docker run -d --network mynetwork --name myapp nginx
     ```

### 8. **Volumes in Docker**
   - **What is a Volume?**: A storage mechanism to persist data.
   - **Create and mount a volume**:
     ```sh
     docker run -d -v /mydata:/data --name myvolumeapp nginx
     ```

### 9. **Docker vs Virtual Machines (VMs)**
   - **Docker**: Uses containerization; lightweight, shares host OS kernel.
   - **VM**: Uses hardware virtualization; includes a full OS and is heavier.

### 10. **Examples for Practice**
   - **Create a containerized web server** using Nginx.
   - **Build a custom Docker image** from a Dockerfile.
   - **Use Docker Compose** to set up a simple web application with a database.

### 11. **Common Docker Scenarios**
   - **Scaling a service**:
     ```sh
     docker-compose up --scale web=3
     ```
   - **Connect two services** (e.g., web app and database) using Docker Compose.

These basics will help your students understand Docker from scratch and enable them to perform hands-on exercises.






### 1. **docker pull**
   Pull an image from Docker Hub.
   ```sh
   docker pull nginx
   ```

### 2. **docker run**
   Run a container from an image.
   ```sh
   docker run -d --name mynginx -p 8080:80 nginx
   ```

### 3. **docker ps**
   List all running containers.
   ```sh
   docker ps
   ```

### 4. **docker ps -a**
   List all containers, including stopped ones.
   ```sh
   docker ps -a
   ```

### 5. **docker stop**
   Stop a running container.
   ```sh
   docker stop mynginx
   ```

### 6. **docker start**
   Start a stopped container.
   ```sh
   docker start mynginx
   ```

### 7. **docker restart**
   Restart a running or stopped container.
   ```sh
   docker restart mynginx
   ```

### 8. **docker rm**
   Remove a stopped container.
   ```sh
   docker rm mynginx
   ```

### 9. **docker rmi**
   Remove an image.
   ```sh
   docker rmi nginx
   ```

### 10. **docker exec**
   Execute a command inside a running container.
   ```sh
   docker exec -it mynginx /bin/bash
   ```

### 11. **docker images**
   List all images on your local machine.
   ```sh
   docker images
   ```

### 12. **docker build**
   Build an image from a Dockerfile.
   ```sh
   docker build -t mynginximage .
   ```

### 13. **docker logs**
   View logs from a container.
   ```sh
   docker logs mynginx
   ```

### 14. **docker network create**
   Create a custom network.
   ```sh
   docker network create mynetwork
   ```

### 15. **docker network ls**
   List all Docker networks.
   ```sh
   docker network ls
   ```

### 16. **docker volume create**
   Create a Docker volume.
   ```sh
   docker volume create myvolume
   ```

### 17. **docker volume ls**
   List all Docker volumes.
   ```sh
   docker volume ls
   ```

### 18. **docker inspect**
   Get detailed information about a container or image.
   ```sh
   docker inspect mynginx
   ```

### 19. **docker tag**
   Tag an image with a different name.
   ```sh
   docker tag mynginximage myrepo/mynginx:latest
   ```

### 20. **docker push**
   Push an image to a Docker repository.
   ```sh
   docker push myrepo/mynginx:latest
   ```

### 21. **docker-compose up**
   Use Docker Compose to start all services defined in a `docker-compose.yml`.
   ```sh
   docker-compose up
   ```

### 22. **docker-compose down**
   Stop and remove containers, networks, volumes, and images created by `docker-compose up`.
   ```sh
   docker-compose down
   ```

### 23. **docker rename**
   Rename a container.
   ```sh
   docker rename mynginx mynewnginx
   ```

### 24. **docker pause**
   Pause a running container.
   ```sh
   docker pause mynginx
   ```

### 25. **docker unpause**
   Unpause a paused container.
   ```sh
   docker unpause mynginx
   ```

### 26. **docker stats**
   Display real-time resource usage statistics for containers.
   ```sh
   docker stats mynginx
   ```

### 27. **docker top**
   Display running processes inside a container.
   ```sh
   docker top mynginx
   ```

### 28. **docker export**
   Export a container's filesystem as a tar archive.
   ```sh
   docker export mynginx > mynginx.tar
   ```

### 29. **docker cp**
   Copy files between a container and the host.
   ```sh
   docker cp mynginx:/usr/share/nginx/html /home/user/nginx_files
   ```

### 30. **docker attach**
   Attach your terminal to a running container.
   ```sh
   docker attach mynginx
   ```

These commands will help you work efficiently with Docker, whether you are building, running, debugging, or managing containers.
