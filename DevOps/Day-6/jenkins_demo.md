
# 🔹 Jenkins Demo Pipelines for Teaching (Real-World)

## 🟢 Demo 1: Basic CI Pipeline (Hello World)

**Best for first Jenkins demo**

### What students learn

* Jenkins job
* Pipeline syntax
* Stages
* Agent

### Demo idea

```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Hello DevOps Students!'
            }
        }
        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
            }
        }
    }
}
```

🎯 **Use case**
Understanding how CI pipelines are structured.

---

## 🟢 Demo 2: GitHub → Jenkins CI Pipeline

**Most important demo**

### What students learn

* Git integration
* SCM polling / webhook
* Code checkout

### Demo idea

```groovy
pipeline {
    agent any
    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/your-org/sample-app.git'
            }
        }
        stage('Build') {
            steps {
                sh 'echo Building application'
            }
        }
    }
}
```

🎯 **Real-world mapping**
Every company starts CI with Git → Jenkins.

---

## 🟡 Demo 3: Maven Build Pipeline (Java App)

**Very common interview scenario**

### What students learn

* Tool configuration
* Maven build
* Artifact generation

### Demo idea

```groovy
pipeline {
    agent any
    tools {
        maven 'maven3'
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/spring-projects/spring-petclinic.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
    }
}
```

🎯 **Real-world use case**
CI for Java microservices.

---

## 🟡 Demo 4: Docker Build & Push Pipeline

**Students love this demo**

### What students learn

* Docker build
* Docker Hub login
* Image push

### Demo idea

```groovy
pipeline {
    agent any
    stages {
        stage('Build Image') {
            steps {
                sh 'docker build -t myapp:v1 .'
            }
        }
        stage('Push Image') {
            steps {
                sh 'docker tag myapp:v1 username/myapp:v1'
                sh 'docker push username/myapp:v1'
            }
        }
    }
}
```

🎯 **Real-world use case**
Containerized CI pipelines.

---

## 🟡 Demo 5: CI + Unit Testing Pipeline

**Good for quality gate explanation**

### What students learn

* Test execution
* Pipeline failure
* Reports

### Demo idea

```groovy
pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                sh 'echo Running unit tests'
                sh 'exit 0'
            }
        }
    }
}
```

👉 Change `exit 0` to `exit 1` to show **pipeline failure**.

---

## 🟠 Demo 6: Jenkins Pipeline with Parameters

**Very useful in real projects**

### What students learn

* Parameterized builds
* Environment selection

### Demo idea

```groovy
pipeline {
    agent any
    parameters {
        choice(name: 'ENV', choices: ['dev', 'qa', 'prod'])
    }
    stages {
        stage('Deploy') {
            steps {
                echo "Deploying to ${params.ENV}"
            }
        }
    }
}
```

🎯 **Use case**
Same pipeline → multiple environments.

---

## 🟠 Demo 7: Jenkins + AWS (EC2 / S3)

**Great DevOps demo**

### What students learn

* Credentials
* AWS CLI
* IAM role usage

### Demo idea

```groovy
pipeline {
    agent any
    stages {
        stage('Upload to S3') {
            steps {
                sh 'aws s3 ls'
            }
        }
    }
}
```

🎯 **Use case**
CI pipelines interacting with cloud.

---

## 🔴 Demo 8: Jenkins → Kubernetes Deployment

**Advanced demo (interview-level)**

### What students learn

* kubectl
* Deployment updates
* CD basics

### Demo idea

```groovy
pipeline {
    agent any
    stages {
        stage('Deploy to K8s') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
            }
        }
    }
}
```

🎯 **Use case**
Continuous Deployment (CD).

---

## 🔴 Demo 9: Jenkins Shared Library (Conceptual)

**Explain, not mandatory to demo live**

### What students learn

* Reusable pipelines
* Enterprise Jenkins usage

Example explanation:

```groovy
@Library('my-shared-lib') _
buildApp()
deployApp()
```

---

# 🧠 Suggested Teaching Flow (2–3 Days)

### Day 1

* Jenkins intro
* Demo 1, 2, 3

### Day 2

* Demo 4 (Docker)
* Demo 6 (Parameters)
* Demo 5 (Failure handling)

### Day 3 (Advanced)

* Demo 7 (AWS)
* Demo 8 (Kubernetes)
* Interview questions

---

# 🎯 Best 3 Demos (If Time Is Limited)

1. **GitHub → Jenkins CI**
2. **Maven + Docker pipeline**
3. **Parameterized deployment**

These cover **90% real-world Jenkins usage**.

---

