

## ✅ What you’ll do

1. Create a small Java project (Maven) with a unit test.
2. Start SonarQube using Docker Compose.
3. Build & test the project (generates JaCoCo coverage).
4. Run Sonar analysis (Maven `sonar:sonar`) and view results in the SonarQube UI.

---

## Prerequisites

* Docker & Docker Compose installed and running.
* Java JDK (11+) and Maven installed on your host (for `mvn`).
* At least **2–4 GB** of RAM available to Docker (SonarQube needs memory).

---

## Project layout (copy this)

Create a folder `sonar-demo` and inside it create files as shown below.

```
sonar-demo/
├── docker-compose.yml
├── pom.xml
└── src
    ├── main
    │   └── java
    │       └── com/example
    │           └── App.java
    └── test
        └── java
            └── com/example
                └── AppTest.java
```

---

### 1) `docker-compose.yml` (SonarQube + Postgres)

Create `docker-compose.yml`:

```yaml
version: "3.8"
services:
  db:
    image: postgres:13
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
      POSTGRES_DB: sonarqube
    volumes:
      - sonarqube_db:/var/lib/postgresql/data
  sonarqube:
    image: sonarqube:9.9-community
    depends_on:
      - db
    ports:
      - "9000:9000"
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://db:5432/sonarqube
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    ulimits:
      nofile:
        soft: 65536
        hard: 65536
volumes:
  sonarqube_db:
```

> **Tip:** If Docker Desktop has limited memory, raise it to ≥ 2GB (prefer 3–4GB) before starting SonarQube.

---

### 2) `pom.xml` (Maven config, JUnit5, JaCoCo)

Create `pom.xml`:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" 
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>sonar-demo</artifactId>
  <version>1.0-SNAPSHOT</version>
  <properties>
    <maven.compiler.source>11</maven.compiler.source>
    <maven.compiler.target>11</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <jacoco.version>0.8.8</jacoco.version>
  </properties>

  <dependencies>
    <!-- JUnit 5 -->
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <version>5.9.2</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <!-- Surefire for tests -->
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>3.0.0-M7</version>
      </plugin>

      <!-- JaCoCo plugin -->
      <plugin>
        <groupId>org.jacoco</groupId>
        <artifactId>jacoco-maven-plugin</artifactId>
        <version>${jacoco.version}</version>
        <executions>
          <!-- prepare agent before tests -->
          <execution>
            <id>prepare-agent</id>
            <goals>
              <goal>prepare-agent</goal>
            </goals>
          </execution>
          <!-- generate report after tests -->
          <execution>
            <id>report</id>
            <phase>test</phase>
            <goals>
              <goal>report</goal>
            </goals>
            <configuration>
              <outputDirectory>${project.build.directory}/site/jacoco</outputDirectory>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
</project>
```

---

### 3) Minimal Java app: `App.java`

Create `src/main/java/com/example/App.java`:

```java
package com.example;

public class App {
    public static int add(int a, int b) {
        return a + b;
    }

    public static void main(String[] args) {
        System.out.println("Hello from sonar-demo");
    }
}
```

---

### 4) Unit test: `AppTest.java`

Create `src/test/java/com/example/AppTest.java`:

```java
package com.example;

import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class AppTest {
    @Test
    void testAdd() {
        assertEquals(5, App.add(2, 3));
    }
}
```

---

## Steps to run the full lab

### A — Start SonarQube

From `sonar-demo` folder:

```bash
docker-compose up -d
```

Wait \~1–2 minutes. Watch logs:

```bash
docker-compose logs -f sonarqube
```

Visit: `http://localhost:9000`
Login (default): `admin` / `admin` → change password if prompted.

---

### B — Create a Sonar project & generate token

1. In Sonar UI: **Projects → Create Project → Manually**.
2. Project key: `sonar-demo` (or any key).
3. In **Administration → Security → Tokens** (or **My Account → Security**), generate a token for this project and copy it (store in `SONAR_TOKEN` below).

---

### C — Build, test, generate JaCoCo report

On your host (in `sonar-demo`):

```bash
mvn clean verify
```

This runs tests and produces JaCoCo report at:

```
target/site/jacoco/jacoco.xml
```

(verify that file exists)

---

### D — Run Sonar analysis (Maven)

Run analysis and upload results to SonarQube:

```bash
mvn sonar:sonar \
  -Dsonar.projectKey=sonar-demo \
  -Dsonar.host.url=http://localhost:9000 \
  -Dsonar.login=YOUR_TOKEN \
  -Dsonar.java.binaries=target/classes \
  -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml
```

Replace `YOUR_TOKEN` with the token you generated in the UI.

**Note:** Passing the token via CLI keeps credentials out of source control.

---

### E — View results

Open Sonar UI → Projects → `sonar-demo`. You’ll see:

* Bugs, Vulnerabilities, Code Smells
* Coverage (from JaCoCo)
* Duplications, Complexity
* Quality Gate status

If the project is new, Sonar will compute analysis and display metrics in the project dashboard.

---

## Optional: Use Dockerized Sonar Scanner (if you don’t want Maven plugin)

If for some reason you cannot run `mvn sonar:sonar`, you can run the official scanner container — but make sure you’ve already built project/classes (`mvn test`):

```bash
docker run --rm \
  -v "$(pwd):/usr/src" \
  -e SONAR_HOST_URL="http://host.docker.internal:9000" \
  -e SONAR_LOGIN="YOUR_TOKEN" \
  sonarsource/sonar-scanner-cli \
  -Dsonar.projectKey=sonar-demo \
  -Dsonar.sources=src/main/java \
  -Dsonar.java.binaries=target/classes \
  -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml
```

> On **Linux**, `host.docker.internal` may not exist. If that fails, run the scanner on the host (`mvn sonar:sonar`) or run the scanner container with `--network host` (Linux) and `-e SONAR_HOST_URL=http://localhost:9000`.

---

## Jenkins CI snippet (optional)

If you want to integrate into Jenkins pipeline:

```groovy
pipeline {
  agent any
  stages {
    stage('Build & Test') {
      steps {
        sh 'mvn clean verify -DskipTests=false'
      }
    }
    stage('SonarQube Analysis') {
      steps {
        withCredentials([string(credentialsId: 'sonar-token-id', variable: 'SONAR_TOKEN')]) {
          sh 'mvn sonar:sonar -Dsonar.host.url=http://sonarqube-server:9000 -Dsonar.login=$SONAR_TOKEN -Dsonar.java.binaries=target/classes -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml'
        }
      }
    }
  }
}
```

---

## Troubleshooting & tips

* **SonarQube won’t start / Out of memory:** increase Docker memory (Docker Desktop) to 3–4 GB or set `SONAR_JAVA_OPTS` environment var in compose.
* **JaCoCo XML missing:** ensure `mvn clean verify` ran and produced `target/site/jacoco/jacoco.xml`. If not, check `jacoco-maven-plugin` config and plugin version.
* **Scanner can’t reach Sonar:** make sure Sonar is accessible at the URL you used (try `curl http://localhost:9000`).
* **Token auth error:** double-check token and don’t use your admin password as token — use generated token or Sonar login.
* **Windows path mounts:** in Docker commands using `$(pwd)`, use `%cd%` or appropriate path syntax on Windows PowerShell.

---

## Quick checklist for students (copy/paste)

1. `docker-compose up -d`
2. Visit `http://localhost:9000` and create project + token
3. `mvn clean verify`
4. `mvn sonar:sonar -Dsonar.projectKey=... -Dsonar.login=... -Dsonar.host.url=...`
5. Open project in Sonar UI → review findings

---

