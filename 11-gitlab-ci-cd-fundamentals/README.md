\# Lesson 11 — GitLab CI/CD Fundamentals



\## 🎯 Objective



The objective of this lesson was to understand the fundamentals of \*\*CI/CD\*\* — Continuous Integration, Continuous Delivery, and Continuous Deployment — and how GitLab implements them through \*\*pipelines, stages, jobs, runners\*\*, and the `.gitlab-ci.yml` file.



The hands-on part created, troubleshot, and successfully ran the \*\*first GitLab CI/CD pipeline\*\*.



\---



\## 📚 Table of Contents



\*\*Concepts\*\*



1\. \[Introduction](#1-introduction)

2\. \[What Does CI/CD Mean?](#2-what-does-cicd-mean)

3\. \[Continuous Integration](#3-continuous-integration)

4\. \[Why Continuous Integration?](#4-why-continuous-integration)

5\. \[Continuous Delivery](#5-continuous-delivery)

6\. \[Continuous Deployment](#6-continuous-deployment)

7\. \[Continuous Delivery vs Continuous Deployment](#7-continuous-delivery-vs-continuous-deployment)

8\. \[GitLab CI/CD](#8-gitlab-cicd)

9\. \[What Is `.gitlab-ci.yml`?](#9-what-is-gitlab-ciyml)

10\. \[What Is a Pipeline?](#10-what-is-a-pipeline)

11\. \[What Is a Stage?](#11-what-is-a-stage)

12\. \[What Is a Job?](#12-what-is-a-job)

13\. \[Stage vs Job](#13-stage-vs-job)

14\. \[What Is a GitLab Runner?](#14-what-is-a-gitlab-runner)

15\. \[Pipeline Architecture](#15-pipeline-architecture)

16\. \[Pipeline Triggers](#16-pipeline-triggers)

17\. \[Real DevOps Example](#17-real-devops-example)

18\. \[GitLab CI/CD and Jenkins](#18-gitlab-cicd-and-jenkins)

19\. \[Jenkins vs GitLab CI/CD](#19-jenkins-vs-gitlab-cicd)



\*\*Hands-On\*\*



20\. \[First GitLab CI/CD Pipeline](#20-hands-on--first-gitlab-cicd-pipeline)

21\. \[Step 1 — Navigate to the Repository](#21-step-1--navigate-to-the-repository)

22\. \[Step 2 — Create Lesson 11 Branch](#22-step-2--create-lesson-11-branch)

23\. \[Step 3 — Create `.gitlab-ci.yml`](#23-step-3--create-gitlab-ciyml)

24\. \[Step 4 — Commit and Push the Pipeline](#24-step-4--commit-and-push-the-pipeline)

25\. \[Troubleshooting the First Pipeline](#25-troubleshooting-the-first-pipeline)

26\. \[Fix the Pipeline Configuration](#26-fix-the-pipeline-configuration)

27\. \[Successful Pipeline](#27-successful-pipeline)

28\. \[Final Pipeline Configuration](#28-final-pipeline-configuration)

29\. \[Important Troubleshooting Lesson](#29-important-troubleshooting-lesson)



\*\*Summary\*\*



30\. \[Important Commands](#30-important-commands)

31\. \[Production CI/CD Architecture](#31-production-cicd-architecture)

32\. \[CI/CD Security](#32-cicd-security)

33\. \[Key Takeaways](#33-key-takeaways)

34\. \[Lesson 11 Completion](#34-lesson-11-completion)



\---



\## 1. Introduction



CI/CD is one of the core concepts of modern DevOps. It automates repetitive software development activities such as:



\- Building applications

\- Running tests

\- Performing quality checks

\- Packaging applications

\- Deploying applications



GitLab provides an \*\*integrated CI/CD platform\*\* that automatically executes these activities when changes are pushed to a repository.



```text

Developer

&#x20;   ↓

GitLab Repository

&#x20;   ↓

.gitlab-ci.yml

&#x20;   ↓

Pipeline

&#x20;   ↓

Jobs

&#x20;   ↓

GitLab Runner

&#x20;   ↓

Command Execution

&#x20;   ↓

Success / Failure

```



\---



\## 2. What Does CI/CD Mean?



| Abbreviation | Meaning |

|---|---|

| \*\*CI\*\* | Continuous Integration |

| \*\*CD\*\* | Continuous Delivery / Continuous Deployment |



\---



\## 3. Continuous Integration



\*\*Continuous Integration\*\* means frequently integrating code changes into a shared repository and \*\*automatically validating\*\* those changes.



```text

Developer

&#x20;   ↓

Feature Branch

&#x20;   ↓

Commit

&#x20;   ↓

Push

&#x20;   ↓

CI Pipeline

&#x20;   ↓

Build

&#x20;   ↓

Test

&#x20;   ↓

Result

```



If a problem is detected, the developer receives \*\*feedback quickly\*\*.



> 💡 \*\*Simple explanation:\*\* Instead of waiting until the end of development to discover that code doesn't work together, CI \*\*continuously checks\*\* every change.



\---



\## 4. Why Continuous Integration?



Consider a project with multiple developers.



\### ❌ Without CI



```text

Developer A ─┐

Developer B ─┤

Developer C ─┼──→ main

Developer D ─┤

Developer E ─┘

```



Everyone assumes their changes work. Later, integration results in:



\- Build failures

\- Test failures

\- Integration problems

\- Compatibility problems



\### ✅ With CI



```text

Developer

&#x20;   ↓

Push

&#x20;   ↓

Automated Pipeline

&#x20;   ↓

Build

&#x20;   ↓

Tests

&#x20;   ↓

Feedback

```



Problems are detected \*\*earlier\*\*, when they're cheaper and easier to fix.



\---



\## 5. Continuous Delivery



\*\*Continuous Delivery\*\* means keeping the application in a state where it \*\*can be released\*\* whenever required.



```text

Code

&#x20;↓

Build

&#x20;↓

Test

&#x20;↓

Quality Check

&#x20;↓

Package

&#x20;↓

Ready for Deployment  ← a human decides when to deploy

```



The deployment itself still requires a \*\*manual approval or trigger\*\*.



\---



\## 6. Continuous Deployment



\*\*Continuous Deployment\*\* goes one step further: after all checks pass, the application is \*\*automatically deployed\*\*.



```text

Code

&#x20;↓

Build

&#x20;↓

Test

&#x20;↓

Security

&#x20;↓

Package

&#x20;↓

Automatic Deployment  ← no human step

```



\---



\## 7. Continuous Delivery vs Continuous Deployment



| Concept | Meaning | Final step |

|---|---|---|

| \*\*Continuous Delivery\*\* | Application is automatically prepared and kept ready for deployment | 👤 Manual deploy |

| \*\*Continuous Deployment\*\* | Application is automatically deployed after a successful pipeline | 🤖 Automatic deploy |



> 🧠 \*\*Easy way to remember:\*\*

> Continuous \*\*Delivery\*\* → \*Ready\* to deploy

> Continuous \*\*Deployment\*\* → \*Actually\* deploy



\---



\## 8. GitLab CI/CD



GitLab provides CI/CD \*\*directly inside GitLab\*\*. The central configuration file is:



```text

.gitlab-ci.yml

```



This file defines what GitLab should do when a pipeline runs.



```text

Developer

&#x20;   ↓

GitLab Repository

&#x20;   ↓

.gitlab-ci.yml

&#x20;   ↓

Pipeline

&#x20;   ↓

Jobs

&#x20;   ↓

GitLab Runner

&#x20;   ↓

Execution

```



\---



\## 9. What Is `.gitlab-ci.yml`?



`.gitlab-ci.yml` is the configuration file that defines GitLab CI/CD pipelines. It lives in the \*\*root\*\* of the repository and tells GitLab:



\- What stages exist

\- What jobs exist

\- Which stage each job belongs to

\- What commands each job should execute



Example:



```yaml

stages:

&#x20; - test



hello-gitlab:

&#x20; stage: test

&#x20; script:

&#x20;   - echo "Hello GitLab CI/CD"

&#x20;   - echo "My first GitLab pipeline"

```



> 💡 YAML structure and `.gitlab-ci.yml` syntax are covered in detail in \*\*Lesson 12\*\*.



\---



\## 10. What Is a Pipeline?



A \*\*pipeline\*\* represents the complete CI/CD workflow.



```text

Pipeline

│

├── Build

├── Test

├── Quality Check

├── Package

└── Deploy

```



> 🏭 \*\*Analogy:\*\* A pipeline is an \*\*automated software factory\*\* — raw source code goes in one end, a tested, deployable application comes out the other.



```text

Source Code → Build → Testing → Quality Check → Packaging → Deployment

```



\---



\## 11. What Is a Stage?



A \*\*stage\*\* is a logical phase of a pipeline.



```yaml

stages:

&#x20; - build

&#x20; - test

&#x20; - deploy

```



```text

Build Stage

&#x20;    ↓

Test Stage

&#x20;    ↓

Deploy Stage

```



Stages run \*\*in order\*\*. A stage can contain \*\*one or more jobs\*\*.



\---



\## 12. What Is a Job?



A \*\*job\*\* is an individual task executed by the CI/CD system.



```text

Pipeline

│

├── build-job

├── unit-test

├── security-scan

└── deploy-job

```



A job contains its commands under `script`:



```yaml

build:

&#x20; script:

&#x20;   - echo "Building application"

```



The job tells the Runner \*\*which commands to execute\*\*.



\---



\## 13. Stage vs Job



| | Stage | Job |

|---|---|---|

| \*\*What it is\*\* | A logical phase of the pipeline | An actual task performed in that phase |

| \*\*Example\*\* | `test` | `unit-test` |

| \*\*Analogy\*\* | 🏢 Department | 📋 Task performed by that department |



> 💡 Jobs in the \*\*same stage\*\* can run in parallel. The next stage starts only after all jobs in the previous stage succeed.



\---



\## 14. What Is a GitLab Runner?



A \*\*GitLab Runner\*\* is the component that \*\*actually executes\*\* CI/CD jobs.



> 👔 \*\*Analogy:\*\* GitLab is the \*\*manager\*\* who assigns work. The Runner is the \*\*worker\*\* who does it and reports back.



```text

GitLab

&#x20;  ↓

"Run this job."

&#x20;  ↓

GitLab Runner

&#x20;  ↓

Executes commands

&#x20;  ↓

Returns result

```



For example, if a job contains:



```yaml

script:

&#x20; - python app.py

```



the Runner provides the environment where `python app.py` is actually executed.



> 💡 On GitLab.com, jobs run on \*\*GitLab-hosted (instance) runners\*\* by default — that's what ran your first pipeline. Later, you'll install and register \*\*your own runner\*\*.



\---



\## 15. Pipeline Architecture



```text

Developer

&#x20;   │

&#x20;   │ git push

&#x20;   ▼

GitLab Repository

&#x20;   │

&#x20;   ▼

.gitlab-ci.yml

&#x20;   │

&#x20;   ▼

Pipeline

&#x20;   │

&#x20;   ▼

Job

&#x20;   │

&#x20;   ▼

GitLab Runner

&#x20;   │

&#x20;   ▼

Command Execution

&#x20;   │

&#x20;   ├── ✅ Success

&#x20;   └── ❌ Failure

```



\---



\## 16. Pipeline Triggers



A pipeline needs an \*\*event\*\* that causes it to run:



| Trigger | Example |

|---|---|

| Git push | Pushing a commit to any branch |

| Merge Request activity | Opening or updating an MR |

| Scheduled pipeline | Nightly build at 2 AM |

| Manual pipeline | Clicking \*\*Run pipeline\*\* in the UI |

| API trigger | Another system starts the pipeline |



For the first hands-on, the pipeline was triggered by \*\*pushing a branch\*\* containing `.gitlab-ci.yml`:



```text

git push → GitLab → Pipeline → Job

```



\---



\## 17. Real DevOps Example



A production CI/CD pipeline for a Java application could eventually look like:



```text

Developer

&#x20;   ↓

GitLab

&#x20;   ↓

Build (Maven)

&#x20;   ↓

JUnit Tests

&#x20;   ↓

SonarQube

&#x20;   ↓

Quality Gate

&#x20;   ↓

Package JAR

&#x20;   ↓

JFrog Artifactory

&#x20;   ↓

Docker Image

&#x20;   ↓

Kubernetes

```



This will be built gradually throughout later lessons.



\---



\## 18. GitLab CI/CD and Jenkins



Jenkins is also commonly used for CI/CD, so it's important to understand the difference.



\*\*Jenkins:\*\*



```text

GitLab

&#x20;  ↓

Webhook

&#x20;  ↓

Jenkins

&#x20;  ↓

Jenkinsfile

&#x20;  ↓

Jenkins Pipeline

```



\*\*GitLab CI/CD:\*\*



```text

GitLab

&#x20;  ↓

.gitlab-ci.yml

&#x20;  ↓

GitLab Pipeline

&#x20;  ↓

GitLab Runner

```



| Tool | What it is |

|---|---|

| \*\*Jenkins\*\* | A dedicated, standalone CI/CD automation server |

| \*\*GitLab CI/CD\*\* | CI/CD built directly into GitLab |



Organizations can use either one, or integrate both into a larger DevOps architecture.



\---



\## 19. Jenkins vs GitLab CI/CD



| Concept | Jenkins | GitLab CI/CD |

|---|---|---|

| Pipeline configuration | `Jenkinsfile` | `.gitlab-ci.yml` |

| Execution | Jenkins agents | GitLab Runner |

| Source control | Integrates with many SCM systems | Native GitLab integration |

| CI/CD platform | Jenkins | GitLab |

| Pipeline UI | Jenkins | GitLab |

| Credentials | Jenkins Credentials | GitLab CI/CD variables, tokens, and integrations |

| Pipeline triggers | Webhooks, SCM polling, other triggers | GitLab events, schedules, APIs, other triggers |



\---



\# 🛠️ Hands-On



\## 20. Hands-On — First GitLab CI/CD Pipeline



\### Objective



Create the first simple GitLab CI/CD pipeline that runs:



```bash

echo "Hello GitLab CI/CD"

echo "My first GitLab pipeline"

```



The goal was to understand the \*\*complete pipeline lifecycle\*\*.



\---



\## 21. Step 1 — Navigate to the Repository



```bash

cd C:\\Users\\ASPL-PUNE\\gitlab-zero-to-production-gitlab

```



\---



\## 22. Step 2 — Create Lesson 11 Branch



Update `main` first:



```bash

git switch main

git pull origin main

```



Create the feature branch:



```bash

git switch -c feature/lesson11-first-pipeline

```



Verify:



```bash

git branch --show-current

```



```text

feature/lesson11-first-pipeline

```



\---



\## 23. Step 3 — Create `.gitlab-ci.yml`



A `.gitlab-ci.yml` file was created in the \*\*repository root\*\*:



```yaml

stages:

&#x20; - test



hello-gitlab:

&#x20; stage: test

&#x20; script:

&#x20;   - echo "Hello GitLab CI/CD"

&#x20;   - echo "My first GitLab pipeline"

```



What each part means:



| Line | Meaning |

|---|---|

| `stages:` → `- test` | The pipeline has one stage, named `test` |

| `hello-gitlab:` | The name of the job |

| `stage: test` | This job belongs to the `test` stage |

| `script:` | The commands the Runner executes, in order |



> ⚠️ YAML is \*\*indentation-sensitive\*\* — use spaces, never tabs.



\---



\## 24. Step 4 — Commit and Push the Pipeline



```bash

git add .gitlab-ci.yml

git commit -m "Add first GitLab CI pipeline"

git push -u origin feature/lesson11-first-pipeline

```



Initial commit:



```text

6e4b4bd Add first GitLab CI pipeline

```



\---



\## 25. Troubleshooting the First Pipeline



After the push, the \*\*Pipelines\*\* page showed \*\*no pipeline\*\*. Instead, GitLab displayed:



```text

Get started with GitLab CI/CD

```



Rather than randomly changing GitLab settings, the \*\*configuration itself\*\* was investigated.



The file contents in the commit were inspected:



```bash

git show 6e4b4bd:.gitlab-ci.yml

```



➡️ \*\*No output\*\* — the file had no content.



The commit statistics confirmed it:



```bash

git show --stat 6e4b4bd

```



```text

.gitlab-ci.yml | 0

1 file changed, 0 insertions(+), 0 deletions(-)

```



🔍 \*\*Root cause:\*\* The committed `.gitlab-ci.yml` was \*\*empty\*\* — most likely the file was created but not saved in the editor before running `git add`. With no configuration, GitLab had nothing to run.



\---



\## 26. Fix the Pipeline Configuration



The file was corrected:



```yaml

stages:

&#x20; - test



hello-gitlab:

&#x20; stage: test

&#x20; script:

&#x20;   - echo "Hello GitLab CI/CD"

&#x20;   - echo "My first GitLab pipeline"

```



Committed and pushed:



```bash

git add .gitlab-ci.yml

git commit -m "Fix first GitLab CI pipeline configuration"

git push

```



\---



\## 27. Successful Pipeline



After the fix, GitLab created and ran the pipeline successfully. ✅



| Field | Value |

|---|---|

| Pipeline | `#2901354488` |

| Status | ✅ \*\*Passed\*\* |

| Branch | `feature/lesson11-first-pipeline` |



```text

.gitlab-ci.yml

&#x20;     ↓

GitLab detects CI configuration

&#x20;     ↓

Pipeline created

&#x20;     ↓

hello-gitlab job

&#x20;     ↓

GitLab Runner

&#x20;     ↓

Commands executed

&#x20;     ↓

Pipeline Passed ✅

```



\---



\## 28. Final Pipeline Configuration



```yaml

stages:

&#x20; - test



hello-gitlab:

&#x20; stage: test

&#x20; script:

&#x20;   - echo "Hello GitLab CI/CD"

&#x20;   - echo "My first GitLab pipeline"

```



```text

Pipeline

&#x20;  │

&#x20;  └── test stage

&#x20;         │

&#x20;         └── hello-gitlab job

&#x20;                 │

&#x20;                 ├── echo "Hello GitLab CI/CD"

&#x20;                 └── echo "My first GitLab pipeline"

```



\---



\## 29. Important Troubleshooting Lesson



> 🧠 \*\*A `.gitlab-ci.yml` file existing in the repository does not mean it contains a valid pipeline configuration.\*\*



The troubleshooting process:



```text

Pipeline not appearing

&#x20;       ↓

Check repository

&#x20;       ↓

Check branch

&#x20;       ↓

Check commit

&#x20;       ↓

Inspect .gitlab-ci.yml

&#x20;       ↓

File was empty ❗

&#x20;       ↓

Fix configuration

&#x20;       ↓

Commit → Push

&#x20;       ↓

Pipeline created

&#x20;       ↓

Pipeline passed ✅

```



> 🔧 \*\*DevOps rule:\*\* Check the actual configuration \*\*before\*\* changing infrastructure or settings.



\### Preventing this next time



| Check | Command / Tool |

|---|---|

| See the file's content before committing | `type .gitlab-ci.yml` (CMD) / `cat .gitlab-ci.yml` (Bash) |

| See exactly what's staged | `git diff --cached` |

| Validate the YAML in GitLab | \*\*Build → Pipeline editor\*\* (shows syntax errors live) |



\---



\# 📋 Summary



\## 30. Important Commands



| Command | Purpose |

|---|---|

| `git branch --show-current` | Check current branch |

| `git status` | Check repository status |

| `git remote -v` | Check remote |

| `git switch -c feature/my-feature` | Create feature branch |

| `git add .gitlab-ci.yml` | Stage CI configuration |

| `git diff --cached` | Review staged changes before committing |

| `git commit -m "Add GitLab CI pipeline"` | Commit |

| `git push -u origin feature/my-feature` | Push branch |

| `git show <commit>:.gitlab-ci.yml` | Inspect CI config stored in a commit |

| `git show --stat <commit>` | Inspect commit statistics |



Example:



```bash

git show 6e4b4bd:.gitlab-ci.yml

```



\---



\## 31. Production CI/CD Architecture



The first pipeline was intentionally simple. A production pipeline can eventually become:



```text

&#x20;                        GitLab

&#x20;                           │

&#x20;                           ▼

&#x20;                    Merge Request

&#x20;                           │

&#x20;                           ▼

&#x20;                   CI/CD Pipeline

&#x20;                           │

&#x20;         ┌─────────────────┼─────────────────┐

&#x20;         ▼                 ▼                 ▼

&#x20;      Build              Test             Security

&#x20;         │                 │                 │

&#x20;         └─────────────────┼─────────────────┘

&#x20;                           ▼

&#x20;                       SonarQube

&#x20;                           │

&#x20;                           ▼

&#x20;                      Quality Gate

&#x20;                           │

&#x20;                           ▼

&#x20;                   Package Artifact

&#x20;                           │

&#x20;                           ▼

&#x20;                   JFrog Artifactory

&#x20;                           │

&#x20;                           ▼

&#x20;                      Docker Image

&#x20;                           │

&#x20;                           ▼

&#x20;                      Kubernetes

```



Later lessons will build these concepts one step at a time.



\---



\## 32. CI/CD Security



CI/CD pipelines can access important resources:



\- Source code

\- Credentials

\- Cloud infrastructure

\- Artifact repositories

\- Container registries

\- Deployment environments



Therefore, \*\*CI/CD security is critical\*\*:



```text

Least Privilege

&#x20;     +

Secure Credentials

&#x20;     +

Protected Branches

&#x20;     +

Code Review

&#x20;     +

Automated Testing

&#x20;     +

Security Scanning

```



These concepts will be expanded in later lessons.



\---



\## 33. Key Takeaways



After completing Lesson 11, I understand:



\- \[x] What CI means

\- \[x] What CD means

\- \[x] The difference between Continuous Delivery and Continuous Deployment

\- \[x] What a GitLab pipeline is

\- \[x] What a stage is

\- \[x] What a job is

\- \[x] What a GitLab Runner does

\- \[x] What `.gitlab-ci.yml` is

\- \[x] How a Git push triggers a pipeline

\- \[x] How jobs execute commands

\- \[x] The basic difference between Jenkins and GitLab CI/CD

\- \[x] How to create a basic GitLab CI/CD pipeline

\- \[x] How to troubleshoot an empty `.gitlab-ci.yml`

\- \[x] How to inspect a CI configuration stored in a Git commit

\- \[x] How a pipeline flows from source code to Runner execution



\---



\## 34. Lesson 11 Completion



| Item | Status |

|---|---|

| GitLab CI/CD theory | ✅ |

| CI understanding | ✅ |

| CD understanding | ✅ |

| Pipeline, stage, job concepts | ✅ |

| Runner understanding | ✅ |

| `.gitlab-ci.yml` | ✅ |

| First pipeline | ✅ |

| Pipeline troubleshooting | ✅ |

| GitLab Runner execution | ✅ |

| Successful pipeline | ✅ |

| Documentation | ✅ |



\*\*Successful pipeline:\*\*



```text

Pipeline #2901354488

Status:  Passed ✅

Branch:  feature/lesson11-first-pipeline

```



\---



\### ✅ Status: Lesson 11 — Completed



