
Running petclinic locally
Petclinic is a Spring Boot application built using Maven or Gradle. 

You can build a jar file and run it from the command line (it should work just as well with Java 17 or newer):

Create Ec2 t2 micro then download git and JDK17 using yum or dnf.

then run below commands

```````
git clone https://github.com/spring-projects/spring-petclinic.git
cd spring-petclinic
./mvnw clean package -Dmaven.test.skip=true
java -jar target/*.jar

``````


You can then access petclinic at http://ec2_public_ip:8080/


<img width="2084" height="1334" alt="image" src="https://github.com/user-attachments/assets/1369b7f7-e1fe-41a3-84d2-a03db2eb59ce" />


