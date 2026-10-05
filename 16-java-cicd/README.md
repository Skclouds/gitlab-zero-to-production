Lesson 16 — Java CI/CD with GitLab



\## Objective



The objective of this lesson is to implement a real Java CI/CD pipeline using GitLab CI/CD, Maven, JUnit, and a self-managed Windows GitLab Runner.



By the end of this lesson, the following workflow was implemented:



Developer

&#x20;   ↓

GitLab Repository

&#x20;   ↓

GitLab CI/CD Pipeline

&#x20;   ↓

Windows GitLab Runner

&#x20;   ↓

Maven Build

&#x20;   ↓

JUnit Tests

&#x20;   ↓

Maven Package

&#x20;   ↓

JAR Artifact

&#x20;   ↓

Download from GitLab



\---



\# 1. Technologies Used



| Technology | Purpose |

|---|---|

| Java 21 LTS | Application development |

| Maven | Build and dependency management |

| JUnit 5 | Unit testing |

| GitLab | Source code and CI/CD |

| GitLab Runner | CI/CD job execution |

| Windows | Self-managed Runner environment |

| Git | Version control |



\---



\# 2. GitLab Branch



The lesson was implemented on:



```text

feature/lesson16-java-cicd



3\. Java Project Structure

The Java application was created using a standard Maven project structure.

student-management/

│

├── .gitignore

├── pom.xml

│

└── src/

&#x20;   ├── main/

&#x20;   │   └── java/

&#x20;   │       └── com/

&#x20;   │           └── gitlab/

&#x20;   │               └── student/

&#x20;   │                   └── StudentApp.java

&#x20;   │

&#x20;   └── test/

&#x20;       └── java/

&#x20;           └── com/

&#x20;               └── gitlab/

&#x20;                   └── student/

&#x20;                       └── StudentAppTest.java



4\. Maven pom.xml

The pom.xml is the main configuration file for the Maven project.

It defines:

\- Project identity

\- Artifact name

\- Project version

\- Java version

\- Dependencies

\- Build configuration

The project was configured for Java 21.

<properties>

&#x20;   <maven.compiler.source>21</maven.compiler.source>

&#x20;   <maven.compiler.target>21</maven.compiler.target>

&#x20;   <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>

</properties>



5\. Maven Project Identity

The project uses:

<groupId>com.gitlab.student</groupId>

<artifactId>student-management</artifactId>

<version>1.0-SNAPSHOT</version>



groupId

Identifies the organization or project group.

artifactId

Identifies the application.

version

Defines the current project version.

The resulting JAR was:

student-management-1.0-SNAPSHOT.jar



6\. JUnit Dependency

JUnit 5 was added as a test dependency.

<dependency>

&#x20;   <groupId>org.junit.jupiter</groupId>

&#x20;   <artifactId>junit-jupiter</artifactId>

&#x20;   <version>5.13.4</version>

&#x20;   <scope>test</scope>

</dependency>



The test scope means the dependency is required for testing rather than being part of the production application.

7\. Java Application

The main application was created at:

src/main/java/com/gitlab/student/StudentApp.java



Implementation:

package com.gitlab.student;



public class StudentApp {



&#x20;   public static void main(String\[] args) {

&#x20;       System.out.println("Student Management Application");

&#x20;       System.out.println("Java CI/CD with GitLab");

&#x20;   }

}



8\. Unit Test

The test was created at:

src/test/java/com/gitlab/student/StudentAppTest.java



Implementation:

package com.gitlab.student;



import org.junit.jupiter.api.Test;



import static org.junit.jupiter.api.Assertions.assertEquals;



class StudentAppTest {



&#x20;   @Test

&#x20;   void applicationNameShouldBeCorrect() {



&#x20;       String applicationName = "Student Management Application";



&#x20;       assertEquals(

&#x20;               "Student Management Application",

&#x20;               applicationName

&#x20;       );

&#x20;   }

}



The test validates that the expected application name matches the actual value.

9\. .gitignore

Maven generates build output inside the target directory.

The project therefore uses:

target/



This prevents generated Maven build files from being committed to Git.

The repository contains source code and configuration, while CI generates the build output.

10\. Maven Compile

The first Maven command tested was:

mvn compile



This compiles the Java source code.

The source:

StudentApp.java



was converted into:

StudentApp.class



under:

target/classes/



The resulting structure included:

target/

└── classes/

&#x20;   └── com/

&#x20;       └── gitlab/

&#x20;           └── student/

&#x20;               └── StudentApp.class



11\. Maven Test

The unit tests were executed using:

mvn test



Maven:

1\. Compiled the application

2\. Compiled the test code

3\. Executed the JUnit test

4\. Generated test reports

The test successfully passed.

12\. Maven Package

The application was packaged using:

mvn package



Maven generated:

target/student-management-1.0-SNAPSHOT.jar



The JAR was inspected using:

jar tf target\\student-management-1.0-SNAPSHOT.jar



The JAR contained:

com/gitlab/student/StudentApp.class



This verified that the compiled application was actually included inside the JAR.

13\. Maven Clean and Verify

The Maven lifecycle was also explored using:

mvn clean verify



clean removes previous build output.

verify runs the required earlier lifecycle phases and performs verification configured for the project.

The Maven lifecycle concept was studied as:

validate

&#x20;   ↓

compile

&#x20;   ↓

test

&#x20;   ↓

package

&#x20;   ↓

verify

&#x20;   ↓

install

&#x20;   ↓

deploy



14\. First GitLab CI/CD Pipeline

The first Java pipeline was created using .gitlab-ci.yml.

Initial pipeline:

stages:

&#x20; - build

&#x20; - test

&#x20; - package



build:

&#x20; stage: build

&#x20; tags:

&#x20;   - windows

&#x20; script:

&#x20;   - cd student-management

&#x20;   - mvn compile



test:

&#x20; stage: test

&#x20; tags:

&#x20;   - windows

&#x20; script:

&#x20;   - cd student-management

&#x20;   - mvn test



package:

&#x20; stage: package

&#x20; tags:

&#x20;   - windows

&#x20; script:

&#x20;   - cd student-management

&#x20;   - mvn package



15\. GitLab Runner

The pipeline used the previously configured self-managed Windows GitLab Runner.

Runner tag:

windows



The jobs therefore included:

tags:

&#x20; - windows



This tells GitLab to select the Windows Runner for these jobs.

The architecture was:

GitLab

&#x20;  │

&#x20;  │ CI/CD Job

&#x20;  ↓

Windows GitLab Runner

&#x20;  │

&#x20;  ├── Java 21

&#x20;  └── Maven



16\. Pipeline Stages

The Java pipeline contains three stages:

BUILD

&#x20; ↓

TEST

&#x20; ↓

PACKAGE



Build

mvn compile



Compiles the Java application.

Test

mvn test



Runs the JUnit tests.

Package

mvn package



Creates the JAR file.

17\. Successful Java CI/CD Pipeline

The first complete Java pipeline successfully executed:

BUILD      ✅

TEST       ✅

PACKAGE    ✅



The pipeline successfully executed Maven commands on the self-managed Windows GitLab Runner.

This confirmed that the Java/Maven environment was working correctly inside GitLab CI/CD.

18\. GitLab CI Artifacts

Initially, Maven generated the JAR only inside the Runner workspace.

GitLab was then configured to preserve the JAR as a CI artifact.

The package job was configured as:

package:

&#x20; stage: package

&#x20; tags:

&#x20;   - windows

&#x20; script:

&#x20;   - cd student-management

&#x20;   - mvn package

&#x20; artifacts:

&#x20;   paths:

&#x20;     - student-management/target/\*.jar



The artifact path:

student-management/target/\*.jar



tells GitLab to collect generated JAR files from the Maven target directory.

The JAR could then be downloaded from GitLab after the pipeline completed.

19\. Maven Dependency Cache

Maven dependencies were configured to use a project-local repository:

variables:

&#x20; MAVEN\_OPTS: "-Dmaven.repo.local=.m2/repository"



GitLab caching was configured with:

cache:

&#x20; paths:

&#x20;   - .m2/repository



The concept is:

First Pipeline

&#x20;     ↓

Download Maven dependencies

&#x20;     ↓

.m2/repository

&#x20;     ↓

GitLab Cache



Future Pipeline

&#x20;     ↓

Reuse cached dependencies

&#x20;     ↓

Reduced dependency downloads



Caching can help improve pipeline performance.

20\. Java and Maven Environment Verification

The CI environment was verified using:

\- java --version

\- mvn --version



This is useful because the local developer environment and CI Runner environment are separate.

The pipeline should verify the environment in which the application is actually being built.

Concept:

Developer Machine

&#x20;     ↓

Java + Maven



&#x20;       ≠



GitLab Runner

&#x20;     ↓

Java + Maven



Environment verification helps diagnose:

"It works on my machine"



type problems.

21\. Pipeline Failure Handling

A Java unit test was intentionally changed to fail.

The test compared:

Expected: Wrong Application Name

Actual:   Student Management Application



This caused the test stage to fail.

Expected CI/CD behavior:

BUILD      ✅

&#x20;  ↓

TEST       ❌

&#x20;  ↓

PACKAGE    ⏹️



This demonstrated an important CI/CD principle:

A failed quality check should prevent later stages from continuing.



The test was then corrected and the pipeline returned to a successful state.

22\. JUnit Reports

Maven Surefire generates JUnit-compatible XML reports under:

student-management/target/surefire-reports/



GitLab can consume these reports using:

artifacts:

&#x20; when: always

&#x20; reports:

&#x20;   junit:

&#x20;     - student-management/target/surefire-reports/\*.xml



The use of:

when: always



is important because test reports should still be collected when the test job fails.

23\. Final CI/CD Concept

The complete lesson demonstrated:

Developer

&#x20;   │

&#x20;   ▼

GitLab Repository

&#x20;   │

&#x20;   ▼

.gitlab-ci.yml

&#x20;   │

&#x20;   ▼

GitLab Pipeline

&#x20;   │

&#x20;   ▼

Windows GitLab Runner

&#x20;   │

&#x20;   ├── Java 21

&#x20;   ├── Maven

&#x20;   │

&#x20;   ▼

Maven Compile

&#x20;   │

&#x20;   ▼

JUnit Tests

&#x20;   │

&#x20;   ▼

Maven Package

&#x20;   │

&#x20;   ▼

student-management-1.0-SNAPSHOT.jar

&#x20;   │

&#x20;   ▼

GitLab CI Artifact



24\. Commands Practiced

Java

java --version

javac --version



Maven

mvn --version

mvn compile

mvn test

mvn package

mvn clean verify



JAR inspection

jar tf target\\student-management-1.0-SNAPSHOT.jar



Git

git status

git add .

git commit

git push



25\. Key Learnings

After completing this lesson, I understand:

\- How to create a Maven Java project

\- The purpose of pom.xml

\- Maven project structure

\- Java compilation with Maven

\- JUnit unit testing

\- Maven lifecycle

\- Maven compile

\- Maven test

\- Maven package

\- Maven clean

\- Maven verify

\- .gitignore for Maven projects

\- GitLab CI/CD for Java

\- GitLab Runner execution

\- Runner tags

\- GitLab CI artifacts

\- Maven dependency caching

\- JUnit test reports

\- CI environment verification

\- CI pipeline failure handling

\- JAR generation and inspection

26\. Lesson 16 Outcome

A complete Java Maven application was successfully integrated with GitLab CI/CD.

The final workflow is:

Java Application

&#x20;      ↓

Maven

&#x20;      ↓

JUnit

&#x20;      ↓

GitLab CI/CD

&#x20;      ↓

Windows Runner

&#x20;      ↓

Build

&#x20;      ↓

Test

&#x20;      ↓

Package

&#x20;      ↓

JAR

&#x20;      ↓

GitLab Artifact



This provides the foundation for integrating Java CI/CD with the advanced DevOps tools covered later in the roadmap, including SonarQube, GitLab security scanning, JFrog Artifactory, Docker, Terraform, and Kubernetes.

