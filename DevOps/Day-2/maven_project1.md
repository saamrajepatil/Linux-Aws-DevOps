✅  1: Run Maven Project in Eclipse IDE
🔧 Prerequisites
•	Install Java (JDK)
•	Install Eclipse IDE for Java Developers: https://www.eclipse.org/downloads/
•	Ensure Maven is installed or bundled with Eclipse
🪜 Steps:
1.	Open Eclipse
2.	Go to: File → New → Project → Maven Project
3.	Select:
o	Create a simple project (skip archetype selection)
4.	Enter:
o	Group ID: com.example
o	Artifact ID: HelloMaven
5.	Finish → Maven project gets created with default structure
6.	Right-click src/main/java → New → Class → App.java
7.	Add:
java

package com.example;

public class App {
    public static void main(String[] args) {
        System.out.println("Hello from Maven in Eclipse!");
    }
}
8.	Right-click project → Run As → Java Application
✅ Output:

Hello from Maven in Eclipse!
________________________________________

