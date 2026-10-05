# Lesson 16 — Java CI/CD with GitLab

## 🎯 Objective

The objective of this lesson was to implement a **real Java CI/CD pipeline** using GitLab CI/CD, Maven, JUnit, and the self-managed Windows GitLab Runner from Lesson 14.

Workflow implemented:

```text
Developer
    ↓
GitLab Repository
    ↓
GitLab CI/CD Pipeline
    ↓
Windows GitLab Runner
    ↓
Maven Build
    ↓
JUnit Tests
    ↓
Maven Package
    ↓
JAR Artifact
    ↓
Download from GitLab
```

---

## 📚 Table of Contents

**Setup**

1. [Technologies Used](#1-technologies-used)
2. [GitLab Branch](#2-gitlab-branch)
3. [Java Project Structure](#3-java-project-structure)
4. [Maven `pom.xml`](#4-maven-pomxml)
5. [Maven Project Identity](#5-maven-project-identity)
6. [JUnit Dependency](#6-junit-dependency)
7. [Java Application](#7-java-application)
8. [Unit Test](#8-unit-test)
9. [`.gitignore`](#9-gitignore)

**Maven Locally**

10. [Maven Compile](#10-maven-compile)
11. [Maven Test](#11-maven-test)
12. [Maven Package](#12-maven-package)
13. [Maven Clean, Verify, and the Lifecycle](#13-maven-clean-verify-and-the-lifecycle)

**GitLab CI/CD**

14. [First Java Pipeline](#14-first-java-pipeline)
15. [GitLab Runner](#15-gitlab-runner)
16. [Pipeline Stages](#16-pipeline-stages)
17. [Successful Java Pipeline](#17-successful-java-pipeline)
18. [GitLab CI Artifacts](#18-gitlab-ci-artifacts)
19. [Maven Dependency Cache](#19-maven-dependency-cache)
20. [Java and Maven Environment Verification](#20-java-and-maven-environment-verification)
21. [Pipeline Failure Handling](#21-pipeline-failure-handling)
22. [JUnit Reports](#22-junit-reports)
23. [Consolidated `.gitlab-ci.yml`](#23-consolidated-gitlab-ciyml)

**Summary**

24. [Final CI/CD Concept](#24-final-cicd-concept)
25. [Commands Practiced](#25-commands-practiced)
26. [Key Learnings](#26-key-learnings)
27. [Lesson 16 Outcome](#27-lesson-16-outcome)

---

## 1. Technologies Used

| Technology | Purpose |
|---|---|
| Java 21 LTS | Application development |
| Maven | Build and dependency management |
| JUnit 5 | Unit testing |
| GitLab | Source code and CI/CD |
| GitLab Runner | CI/CD job execution |
| Windows | Self-managed Runner environment |
| Git | Version control |

---

## 2. GitLab Branch

```text
feature/lesson16-java-cicd
```

---

## 3. Java Project Structure

The application uses the **standard Maven project structure**:

```text
student-management/
│
├── .gitignore
├── pom.xml
│
└── src/
    ├── main/
    │   └── java/
    │       └── com/
    │           └── gitlab/
    │               └── student/
    │                   └── StudentApp.java
    │
    └── test/
        └── java/
            └── com/
                └── gitlab/
                    └── student/
                        └── StudentAppTest.java
```

| Folder | Contains |
|---|---|
| `src/main/java` | Application code (goes into the JAR) |
| `src/test/java` | Test code (run by Maven, not shipped) |

> 💡 The folder path `com/gitlab/student` must match the Java `package com.gitlab.student;` declaration.

---

## 4. Maven `pom.xml`

`pom.xml` (**Project Object Model**) is the main Maven configuration file. It defines:

- Project identity
- Artifact name
- Project version
- Java version
- Dependencies
- Build configuration

The project targets **Java 21**:

```xml
<properties>
    <maven.compiler.source>21</maven.compiler.source>
    <maven.compiler.target>21</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>
```

> 💡 On modern Maven, `<maven.compiler.release>21</maven.compiler.release>` can replace `source` + `target` and is slightly safer.

---

## 5. Maven Project Identity

```xml
<groupId>com.gitlab.student</groupId>
<artifactId>student-management</artifactId>
<version>1.0-SNAPSHOT</version>
```

| Element | Identifies | Value |
|---|---|---|
| `groupId` | Organization or project group | `com.gitlab.student` |
| `artifactId` | The application | `student-management` |
| `version` | Current project version | `1.0-SNAPSHOT` |

The resulting JAR name is built from `artifactId` + `version`:

```text
student-management-1.0-SNAPSHOT.jar
```

> 💡 `SNAPSHOT` means "work in progress" — a development version, not a final release.

---

## 6. JUnit Dependency

JUnit 5 was added as a **test** dependency:

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.13.4</version>
    <scope>test</scope>
</dependency>
```

`<scope>test</scope>` means the dependency is used **only for testing** — it's not part of the production application or JAR.

> 💡 JUnit 5 tests are run by the **Maven Surefire plugin**. Older Surefire versions silently run **zero** tests with JUnit 5. If tests ever "pass" with `Tests run: 0`, pin a recent Surefire version in `<build><plugins>`.

---

## 7. Java Application

`src/main/java/com/gitlab/student/StudentApp.java`:

```java
package com.gitlab.student;

public class StudentApp {

    public static void main(String[] args) {
        System.out.println("Student Management Application");
        System.out.println("Java CI/CD with GitLab");
    }
}
```

---

## 8. Unit Test

`src/test/java/com/gitlab/student/StudentAppTest.java`:

```java
package com.gitlab.student;

import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;

class StudentAppTest {

    @Test
    void applicationNameShouldBeCorrect() {

        String applicationName = "Student Management Application";

        assertEquals(
                "Student Management Application",
                applicationName
        );
    }
}
```

The test validates that the expected application name matches the actual value.

> ⚠️ **Improvement for later:** This test compares a string to itself, so it doesn't actually check `StudentApp`. A real test calls application code — for example, add a method to `StudentApp`:
> ```java
> public static String getApplicationName() {
>     return "Student Management Application";
> }
> ```
> and test it:
> ```java
> assertEquals("Student Management Application", StudentApp.getApplicationName());
> ```
> Now the test fails if someone changes the application's behavior — which is the whole point of CI.

---

## 9. `.gitignore`

Maven writes build output to the `target/` directory, so it's excluded from Git:

```gitignore
target/
.m2/
```

> 💡 `.m2/` is added because the CI cache configuration (section 19) creates a local Maven repository inside the project folder.

The repository stores **source code and configuration**; CI **generates** the build output.

---

# 🔨 Maven Locally

## 10. Maven Compile

```bash
mvn compile
```

Compiles the Java source code: `StudentApp.java` → `StudentApp.class`

```text
target/
└── classes/
    └── com/
        └── gitlab/
            └── student/
                └── StudentApp.class
```

---

## 11. Maven Test

```bash
mvn test
```

Maven:

1. Compiled the application
2. Compiled the test code
3. Executed the JUnit test
4. Generated test reports

✅ The test passed.

---

## 12. Maven Package

```bash
mvn package
```

Generated:

```text
target/student-management-1.0-SNAPSHOT.jar
```

The JAR was inspected:

```bash
jar tf target\student-management-1.0-SNAPSHOT.jar
```

It contained:

```text
com/gitlab/student/StudentApp.class
```

✅ This verified the compiled application was actually inside the JAR.

> 💡 A JAR is just a **ZIP file** of compiled classes plus metadata — `jar tf` lists its contents.

---

## 13. Maven Clean, Verify, and the Lifecycle

```bash
mvn clean verify
```

| Command | What it does |
|---|---|
| `clean` | Deletes previous build output (`target/`) |
| `verify` | Runs all earlier phases, then any configured verification checks |

### The Maven lifecycle

```text
validate
    ↓
compile
    ↓
test
    ↓
package
    ↓
verify
    ↓
install
    ↓
deploy
```

> 🧠 **Key rule:** Running a phase runs **every phase before it**. `mvn package` automatically runs `validate → compile → test → package`.

| Phase | Meaning |
|---|---|
| `install` | Copies the JAR to your local `~/.m2` repository |
| `deploy` | Uploads the JAR to a remote repository (e.g. JFrog Artifactory — later lesson) |

---

# ⚙️ GitLab CI/CD

## 14. First Java Pipeline

```yaml
stages:
  - build
  - test
  - package

build:
  stage: build
  tags:
    - windows
  script:
    - cd student-management
    - mvn compile

test:
  stage: test
  tags:
    - windows
  script:
    - cd student-management
    - mvn test

package:
  stage: package
  tags:
    - windows
  script:
    - cd student-management
    - mvn package
```

> 💡 Each job starts in the repository root, so every job needs `cd student-management` — the working directory does not carry over between jobs.

---

## 15. GitLab Runner

The pipeline used the self-managed Windows Runner from Lesson 14, selected by tag:

```yaml
tags:
  - windows
```

```text
GitLab
   │
   │ CI/CD Job
   ↓
Windows GitLab Runner (shell executor)
   │
   ├── Java 21
   └── Maven
```

> ⚠️ With the **Shell** executor, Java 21 and Maven must be installed on the Runner machine and on the `PATH` of the account the Runner service uses (Local System by default).

---

## 16. Pipeline Stages

```text
BUILD
  ↓
TEST
  ↓
PACKAGE
```

| Stage | Command | Purpose |
|---|---|---|
| Build | `mvn compile` | Compiles the Java application |
| Test | `mvn test` | Runs the JUnit tests |
| Package | `mvn package` | Creates the JAR file |

> 💡 Because of the lifecycle rule (section 13), `mvn test` recompiles and `mvn package` re-runs the tests. That's fine for learning. In larger projects, teams often use `mvn package -DskipTests` in the package job, since tests already passed in the previous stage.

---

## 17. Successful Java Pipeline

```text
BUILD      ✅
TEST       ✅
PACKAGE    ✅
```

The pipeline successfully executed Maven on the self-managed Windows Runner, confirming the Java/Maven environment works inside GitLab CI/CD.

---

## 18. GitLab CI Artifacts

Initially, the JAR existed only inside the Runner's workspace. GitLab was configured to **keep it as a CI artifact**:

```yaml
package:
  stage: package
  tags:
    - windows
  script:
    - cd student-management
    - mvn package
  artifacts:
    paths:
      - student-management/target/*.jar
    expire_in: 1 week
```

The path `student-management/target/*.jar` tells GitLab to collect all generated JAR files. After the pipeline finishes, the JAR can be **downloaded** from the job page.

> 💡 Artifact paths are relative to the **repository root**, even though the script did `cd student-management`.
> 💡 `expire_in` controls how long GitLab keeps the artifact. Without it, the instance default applies.

---

## 19. Maven Dependency Cache

Maven was told to store dependencies in a project-local repository, and GitLab was told to cache it:

```yaml
variables:
  MAVEN_OPTS: "-Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository"

cache:
  paths:
    - .m2/repository
```

```text
First Pipeline
      ↓
Download Maven dependencies
      ↓
.m2/repository
      ↓
GitLab Cache

Future Pipeline
      ↓
Reuse cached dependencies
      ↓
Reduced dependency downloads ⚡
```

> ⚠️ **Path must match:** Use `$CI_PROJECT_DIR/.m2/repository` (an absolute path), **not** `.m2/repository`. Because each job runs `cd student-management`, a relative path would put the dependencies in `student-management/.m2/repository` — but the cache saves `.m2/repository` at the repository root. The two paths wouldn't match, and nothing would be cached.

> 💡 With a **Shell** executor, Maven's default `~/.m2` folder already persists on the Runner machine between jobs. Explicit caching matters most with **Docker/Kubernetes** executors, where every job starts in a fresh container.

### Cache vs Artifacts

| | Cache | Artifacts |
|---|---|---|
| Purpose | Speed up future pipelines | Keep job **outputs** |
| Example | Maven dependencies | The built JAR, test reports |
| Downloadable from GitLab UI | ❌ | ✅ |
| Guaranteed to exist | ❌ Best effort | ✅ Until expiry |

---

## 20. Java and Maven Environment Verification

The CI environment was verified with:

```yaml
script:
  - java --version
  - mvn --version
```

The **developer machine** and the **Runner** are separate environments:

```text
Developer Machine          GitLab Runner
   Java + Maven      ≠       Java + Maven
```

Printing versions in the job log helps diagnose **"it works on my machine"** problems.

---

## 21. Pipeline Failure Handling

A unit test was **intentionally broken**:

```text
Expected: Wrong Application Name
Actual:   Student Management Application
```

Result:

```text
BUILD      ✅
   ↓
TEST       ❌
   ↓
PACKAGE    ⏹️ (skipped)
```

> 🧠 **CI/CD principle:** A failed quality check must **stop** later stages. A broken build should never be packaged or deployed.

The test was then corrected, and the pipeline returned to green. ✅

---

## 22. JUnit Reports

Maven Surefire writes JUnit-compatible XML reports to:

```text
student-management/target/surefire-reports/
```

GitLab reads them with:

```yaml
test:
  stage: test
  tags:
    - windows
  script:
    - cd student-management
    - mvn test
  artifacts:
    when: always
    reports:
      junit:
        - student-management/target/surefire-reports/*.xml
```

GitLab then shows a **Tests** tab on the pipeline, listing each test with pass/fail status — and shows test results directly in Merge Requests.

> ⚠️ `when: always` is important: by default, artifacts are only collected when a job **succeeds** — but the test report is most useful precisely when tests **fail**.

---

## 23. Consolidated `.gitlab-ci.yml`

All the pieces from this lesson combined into one file:

```yaml
variables:
  MAVEN_OPTS: "-Dmaven.repo.local=$CI_PROJECT_DIR/.m2/repository"

default:
  tags:
    - windows
  cache:
    paths:
      - .m2/repository
  before_script:
    - java --version
    - mvn --version
    - cd student-management

stages:
  - build
  - test
  - package

build:
  stage: build
  script:
    - mvn compile

test:
  stage: test
  script:
    - mvn test
  artifacts:
    when: always
    reports:
      junit:
        - student-management/target/surefire-reports/*.xml

package:
  stage: package
  script:
    - mvn package -DskipTests
  artifacts:
    paths:
      - student-management/target/*.jar
    expire_in: 1 week
```

> 💡 This uses `default` (Lesson 12) to remove repetition: every job gets the `windows` tag, the cache, the version checks, and the `cd`.

---

# 📋 Summary

## 24. Final CI/CD Concept

```text
Developer
    │
    ▼
GitLab Repository
    │
    ▼
.gitlab-ci.yml
    │
    ▼
GitLab Pipeline
    │
    ▼
Windows GitLab Runner
    ├── Java 21
    └── Maven
    │
    ▼
Maven Compile
    │
    ▼
JUnit Tests ──► Test Report (Tests tab)
    │
    ▼
Maven Package
    │
    ▼
student-management-1.0-SNAPSHOT.jar
    │
    ▼
GitLab CI Artifact ⬇️
```

---

## 25. Commands Practiced

| Category | Command | Purpose |
|---|---|---|
| Java | `java --version` | Check Java runtime |
| Java | `javac --version` | Check Java compiler |
| Maven | `mvn --version` | Check Maven |
| Maven | `mvn compile` | Compile source code |
| Maven | `mvn test` | Compile and run tests |
| Maven | `mvn package` | Build the JAR |
| Maven | `mvn clean verify` | Clean build through verification |
| JAR | `jar tf target\student-management-1.0-SNAPSHOT.jar` | List JAR contents |
| Git | `git status` / `git add .` / `git commit` / `git push` | Version control |

---

## 26. Key Learnings

- [x] How to create a Maven Java project
- [x] The purpose of `pom.xml`
- [x] Maven project structure
- [x] Java compilation with Maven
- [x] JUnit unit testing
- [x] The Maven lifecycle
- [x] `mvn compile`, `test`, `package`, `clean`, `verify`
- [x] `.gitignore` for Maven projects
- [x] GitLab CI/CD for Java
- [x] GitLab Runner execution
- [x] Runner tags
- [x] GitLab CI artifacts
- [x] Maven dependency caching
- [x] Cache vs artifacts
- [x] JUnit test reports
- [x] CI environment verification
- [x] CI pipeline failure handling
- [x] JAR generation and inspection

---

## 27. Lesson 16 Outcome

A complete Java Maven application was successfully integrated with GitLab CI/CD:

```text
Java Application
       ↓
Maven
       ↓
JUnit
       ↓
GitLab CI/CD
       ↓
Windows Runner
       ↓
Build → Test → Package
       ↓
JAR
       ↓
GitLab Artifact
```

This is the foundation for integrating Java CI/CD with the advanced DevOps tools later in the roadmap: **SonarQube, GitLab security scanning, JFrog Artifactory, Docker, Terraform, and Kubernetes**.

---

### ✅ Status: Lesson 16 — Completed
