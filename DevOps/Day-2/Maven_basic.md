________________________________________
What is Maven?

Maven is a build automation and project management tool for Java projects, widely used in DevOps pipelines.
•	Helps manage project builds, dependencies, and documentation.
•	Uses a Project Object Model (POM) file (pom.xml) to configure builds.

________________________________________


🔍 Why Maven?

•	Dependency Management: Automatically downloads required libraries from central repositories.
•	Standardization: Enforces a consistent directory and build structure.
•	Integration: Easily integrates with CI/CD tools like Jenkins.
•	Reproducibility: Ensures repeatable builds across environments.

________________________________________

📁 Maven Directory Structure

project-root/
├── src/
│   ├── main/
│   │   └── java/
│   └── test/
│       └── java/
├── target/
└── pom.xml
________________________________________
📘 What is pom.xml?


This XML file is the heart of a Maven project.
Example:
<project xmlns="http://maven.apache.org/POM/4.0.0">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>demo-app</artifactId>
  <version>1.0</version>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
________________________________________
🧩 Important Concepts
1. Group ID, Artifact ID, Version
•	groupId: Organization or company.
•	artifactId: Project or module name.
•	version: Project version.
2. Dependencies
Add external libraries like Spring, JUnit, etc.
3. Plugins
Used for extra tasks like compiling code, packaging jars, running tests.
4. Phases in Maven Lifecycle
•	validate: Validate project structure
•	compile: Compile source code
•	test: Run unit tests
•	package: Package into JAR/WAR
•	verify: Run checks
•	install: Install in local repo
•	deploy: Upload to remote repo
mvn package
________________________________________
🛠️ Basic Maven Commands

Command	Purpose
mvn clean	Deletes /target directory
mvn compile	Compiles source code
mvn test	Runs tests
mvn package	Packages into JAR/WAR
mvn install	Installs to local repo
mvn deploy	Deploys to remote repo
________________________________________
🔁 Maven Repositories


•	Local: On developer machine (~/.m2)
•	Central: Public repo by Apache
•	Remote: Internal company repo (e.g., Nexus, Artifactory)
________________________________________
🔗 DevOps Integration

•	Maven can be integrated into:
o	Jenkins (build job)
o	GitHub Actions
o	Docker (packaging and containerizing Maven apps)
o	SonarQube (code quality)
________________________________________
📦 Maven in CI/CD Pipeline (Example)

1.	Developer pushes code to GitHub.
2.	Jenkins pulls code.
3.	Jenkins runs mvn clean install.
4.	Artifacts are stored or deployed.
5.	Docker image is built using the artifact.
6.	Image pushed to ECR/DockerHub.
________________________________________
💡 Best Practices

•	Always version dependencies.
•	Avoid SNAPSHOT versions in production.
•	Use dependency management to centralize versions.
•	Profile configurations for different environments.
________________________________________

sudo yum install java-17-amazon-corretto-headless
