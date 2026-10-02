\# Lesson 12 — `.gitlab-ci.yml` Deep Dive



\## 🎯 Overview



This lesson focuses on the \*\*structure, syntax, components, and commonly used keywords\*\* of the GitLab CI/CD configuration file:



```text

.gitlab-ci.yml

```



The objective is to understand how GitLab CI/CD pipelines are defined using \*\*YAML\*\*, and how different configuration elements work together to control pipeline execution.



\---



\## 📚 Table of Contents



\*\*Concepts\*\*



1\. \[Learning Objectives](#1-learning-objectives)

2\. \[What Is `.gitlab-ci.yml`?](#2-what-is-gitlab-ciyml)

3\. \[Basic Pipeline Structure](#3-basic-pipeline-structure)

4\. \[`stages`](#4-stages)

5\. \[Jobs](#5-jobs)

6\. \[`stage`](#6-stage)

7\. \[`script`](#7-script)

8\. \[`before\_script`](#8-before\_script)

9\. \[`after\_script`](#9-after\_script)

10\. \[`default`](#10-default)

11\. \[Global vs Job-Level Configuration](#11-global-vs-job-level-configuration)

12\. \[Variables](#12-variables)

13\. \[Why Variables Are Useful](#13-why-variables-are-useful)

14\. \[Configuration Variables vs Secrets](#14-configuration-variables-vs-secrets)

15\. \[GitLab Predefined CI/CD Variables](#15-gitlab-predefined-cicd-variables)

16\. \[Custom Variables vs Predefined Variables](#16-custom-variables-vs-predefined-variables)

17\. \[YAML Comments](#17-yaml-comments)

18\. \[YAML Indentation](#18-yaml-indentation)



\*\*Hands-On\*\*



19\. \[Multi-Stage Pipeline](#19-multi-stage-pipeline)

20\. \[Complete Lesson 12 Pipeline](#20-complete-lesson-12-pipeline)

21\. \[Pipeline Execution Flow](#21-pipeline-execution-flow)

22\. \[Common `.gitlab-ci.yml` Mistakes](#22-common-gitlab-ciyml-mistakes)

23\. \[Hands-On Completed](#23-hands-on-completed)



\*\*Summary\*\*



24\. \[Key Takeaways](#24-key-takeaways)

25\. \[Lesson 12 Completion Checklist](#25-lesson-12-completion-checklist)



\---



\## 1. Learning Objectives



By the end of this lesson, the following concepts were covered:



\- YAML fundamentals used in GitLab CI/CD

\- The structure of `.gitlab-ci.yml`

\- `stages`, jobs, and `stage`

\- `script`, `before\_script`, and `after\_script`

\- `default`

\- Global vs job-level configuration

\- Custom CI/CD variables

\- Predefined GitLab CI/CD variables

\- YAML comments and indentation

\- Common `.gitlab-ci.yml` mistakes

\- Building and executing a \*\*multi-stage pipeline\*\*



\---



\## 2. What Is `.gitlab-ci.yml`?



`.gitlab-ci.yml` is the configuration file GitLab CI/CD uses to define \*\*how a pipeline should execute\*\*.



It contains:



\- Pipeline stages

\- Jobs

\- Commands to execute

\- Variables

\- Common job configuration

\- Job-specific configuration



The file must be placed in the \*\*root directory\*\* of the repository:



```text

gitlab-zero-to-production/

│

├── .gitlab-ci.yml   ← here

├── README.md

└── application files

```



GitLab detects `.gitlab-ci.yml` and uses it to create and run pipelines.



> 🍳 \*\*Analogy:\*\* `.gitlab-ci.yml` is a \*\*recipe\*\*. Stages are the courses (starter → main → dessert), jobs are the dishes, and `script` lines are the cooking steps. The Runner is the chef who follows them.



\---



\## 3. Basic Pipeline Structure



```yaml

stages:

&#x20; - build

&#x20; - test



build-job:

&#x20; stage: build

&#x20; script:

&#x20;   - echo "Building application"



test-job:

&#x20; stage: test

&#x20; script:

&#x20;   - echo "Testing application"

```



Structure:



```text

Pipeline

│

├── Stages

│   ├── build

│   └── test

│

└── Jobs

&#x20;   ├── build-job

&#x20;   └── test-job

```



\---



\## 4. `stages`



The `stages` keyword defines the \*\*execution order\*\* of stages.



```yaml

stages:

&#x20; - build

&#x20; - test

&#x20; - deploy

```



```text

build

&#x20; ↓

test

&#x20; ↓

deploy

```



Example with jobs:



```yaml

stages:

&#x20; - build

&#x20; - test



build-job:

&#x20; stage: build

&#x20; script:

&#x20;   - echo "Build"



unit-test:

&#x20; stage: test

&#x20; script:

&#x20;   - echo "Unit Test"

```



The `test` stage starts \*\*only after\*\* the `build` stage completes successfully.



> 💡 If you don't define `stages` at all, GitLab uses the defaults: `.pre → build → test → deploy → .post`. A job without a `stage` keyword goes to `test`.



\---



\## 5. Jobs



A \*\*job\*\* is a unit of work executed by GitLab CI/CD.



```yaml

build-job:

&#x20; stage: build

&#x20; script:

&#x20;   - echo "Building application"

```



Here, `build-job` is the \*\*job name\*\*. Any top-level key that isn't a reserved keyword (like `stages`, `variables`, `default`) is treated as a job.



A pipeline can contain many jobs:



```yaml

build-job:

&#x20; stage: build

&#x20; script:

&#x20;   - echo "Building"



unit-test:

&#x20; stage: test

&#x20; script:

&#x20;   - echo "Running unit tests"



integration-test:

&#x20; stage: test

&#x20; script:

&#x20;   - echo "Running integration tests"



deploy-job:

&#x20; stage: deploy

&#x20; script:

&#x20;   - echo "Deploying"

```



\---



\## 6. `stage`



The `stage` keyword (singular) \*\*assigns a job to a stage\*\*.



```yaml

stages:

&#x20; - build

&#x20; - test

&#x20; - deploy



build-job:

&#x20; stage: build

```



`build-job` belongs to the `build` stage.



| Keyword | Where | Purpose |

|---|---|---|

| `stages` (plural) | Top level | Defines the list and order of stages |

| `stage` (singular) | Inside a job | Assigns that job to one stage |



\---



\## 7. `script`



The `script` keyword defines the \*\*commands a job executes\*\*. They run in order, on the GitLab Runner.



```yaml

build-job:

&#x20; stage: build

&#x20; script:

&#x20;   - echo "Starting build"

&#x20;   - echo "Building application"

&#x20;   - echo "Build completed"

```



In real projects, the commands depend on the technology:



```yaml

\# Node.js

script:

&#x20; - npm install

&#x20; - npm test

&#x20; - npm run build

```



```yaml

\# Java / Maven

script:

&#x20; - mvn clean test

&#x20; - mvn package

```



```yaml

\# Python

script:

&#x20; - python -m pytest

```



> ⚠️ If any command fails (non-zero exit code), the job \*\*stops and fails\*\* immediately.



\---



\## 8. `before\_script`



`before\_script` defines commands that run \*\*before\*\* the main `script`.



```yaml

build-job:

&#x20; stage: build



&#x20; before\_script:

&#x20;   - echo "Preparing build environment"



&#x20; script:

&#x20;   - echo "Building application"

```



```text

before\_script

&#x20;     ↓

script

```



Useful for common preparation: installing dependencies, logging in to a registry, setting up tools.



> 💡 `before\_script` and `script` run in the \*\*same shell\*\*. If `before\_script` fails, `script` never runs and the job fails.



\---



\## 9. `after\_script`



`after\_script` defines commands that run \*\*after\*\* the main `script`.



```yaml

build-job:

&#x20; stage: build



&#x20; script:

&#x20;   - echo "Building application"



&#x20; after\_script:

&#x20;   - echo "Build cleanup completed"

```



```text

before\_script

&#x20;     ↓

script

&#x20;     ↓

after\_script

```



Useful for cleanup or post-job activities.



> 💡 Key behaviors of `after\_script`:

> - It runs \*\*even if `script` fails\*\* — that's what makes it good for cleanup.

> - It runs in a \*\*separate shell\*\*, so variables exported in `script` are not available.

> - A failure in `after\_script` does \*\*not\*\* change the job's status.



\---



\## 10. `default`



The `default` keyword defines \*\*common configuration\*\* inherited by all jobs.



```yaml

default:

&#x20; before\_script:

&#x20;   - echo "Preparing CI environment"



build-job:

&#x20; stage: build

&#x20; script:

&#x20;   - echo "Building"



test-job:

&#x20; stage: test

&#x20; script:

&#x20;   - echo "Testing"

```



Both `build-job` and `test-job` run `echo "Preparing CI environment"` first — without repeating it in each job.



\---



\## 11. Global vs Job-Level Configuration



| | Global (`default`) | Job-level |

|---|---|---|

| \*\*Where\*\* | Under `default:` | Inside a specific job |

| \*\*Applies to\*\* | All jobs | Only that job |

| \*\*Purpose\*\* | Common behavior | Specific behavior |



Global:



```yaml

default:

&#x20; before\_script:

&#x20;   - echo "Preparing CI environment"

```



Job-level:



```yaml

deploy-job:

&#x20; stage: deploy



&#x20; before\_script:

&#x20;   - echo "Preparing deployment environment"



&#x20; script:

&#x20;   - echo "Deploying application"

```



> ⚠️ \*\*Important:\*\* A job-level setting \*\*replaces\*\* the default — it does \*\*not\*\* add to it. In the example above, `deploy-job` runs only `"Preparing deployment environment"`; the default `"Preparing CI environment"` is skipped for that job.



```text

default before\_script   →  used by jobs that don't define their own

job before\_script       →  overrides default for that job only

```



\---



\## 12. Variables



\*\*Variables\*\* are named values reused throughout the pipeline.



```yaml

variables:

&#x20; APP\_NAME: "student-api"

&#x20; ENVIRONMENT: "development"

```



Referenced with `$`:



```yaml

script:

&#x20; - echo "Application: $APP\_NAME"

&#x20; - echo "Environment: $ENVIRONMENT"

```



Output:



```text

Application: student-api

Environment: development

```



> 💡 Variables can also be defined \*\*inside a job\*\*; a job-level variable overrides a global one with the same name for that job.



\---



\## 13. Why Variables Are Useful



❌ Without variables — the value is repeated:



```yaml

script:

&#x20; - echo "Deploying student-api"

&#x20; - echo "student-api deployment completed"

```



✅ With a variable — change it once, it updates everywhere:



```yaml

variables:

&#x20; APP\_NAME: "student-api"



script:

&#x20; - echo "Deploying $APP\_NAME"

&#x20; - echo "$APP\_NAME deployment completed"

```



\---



\## 14. Configuration Variables vs Secrets



Not every variable is a secret.



| Type | Examples | Where to store |

|---|---|---|

| \*\*Configuration\*\* (not sensitive) | `APP\_NAME`, `ENVIRONMENT` | ✅ `.gitlab-ci.yml` is fine |

| \*\*Secrets\*\* (sensitive) | Passwords, API tokens, access keys, private credentials | ❌ \*\*Never\*\* in `.gitlab-ci.yml` |



> 🔐 Secrets belong in \*\*Settings → CI/CD → Variables\*\* as \*\*protected / masked\*\* variables — covered in a later lesson.



\---



\## 15. GitLab Predefined CI/CD Variables



GitLab \*\*automatically\*\* provides variables describing the current pipeline, project, job, commit, and branch.



| Variable | Contains |

|---|---|

| `CI\_PROJECT\_NAME` | Project name |

| `CI\_PROJECT\_PATH` | Namespace + project (e.g. `user/project`) |

| `CI\_COMMIT\_BRANCH` | Branch name (branch pipelines only) |

| `CI\_COMMIT\_REF\_NAME` | Branch \*\*or\*\* tag name |

| `CI\_COMMIT\_SHA` | Full commit SHA |

| `CI\_COMMIT\_SHORT\_SHA` | First 8 characters of the SHA |

| `CI\_COMMIT\_MESSAGE` | Commit message |

| `CI\_PIPELINE\_ID` | Pipeline ID |

| `CI\_JOB\_ID` | Job ID |

| `CI\_JOB\_NAME` | Job name |



Example:



```yaml

script:

&#x20; - echo "Project: $CI\_PROJECT\_NAME"

&#x20; - echo "Branch: $CI\_COMMIT\_BRANCH"

&#x20; - echo "Commit: $CI\_COMMIT\_SHORT\_SHA"

&#x20; - echo "Pipeline ID: $CI\_PIPELINE\_ID"

```



> ⚠️ `CI\_COMMIT\_BRANCH` is \*\*empty\*\* in Merge Request pipelines and tag pipelines. Use `CI\_COMMIT\_REF\_NAME` when you need a value that's always set.



\---



\## 16. Custom Variables vs Predefined Variables



| | Custom Variables | Predefined Variables |

|---|---|---|

| \*\*Defined by\*\* | You (in `.gitlab-ci.yml` or settings) | GitLab, automatically |

| \*\*Example\*\* | `$APP\_NAME`, `$ENVIRONMENT` | `$CI\_PROJECT\_NAME`, `$CI\_PIPELINE\_ID` |

| \*\*Naming\*\* | Your choice | Usually start with `CI\_` or `GITLAB\_` |



> 💡 Avoid naming your own variables with the `CI\_` prefix to prevent clashes.



\---



\## 17. YAML Comments



Comments start with `#` and are \*\*ignored\*\* during execution:



```yaml

\# Define pipeline stages

stages:

&#x20; - build

&#x20; - test

&#x20; - deploy

```



They document the pipeline for other developers and DevOps engineers.



\---



\## 18. YAML Indentation



YAML uses \*\*indentation\*\* to represent structure:



```yaml

build-job:

&#x20; stage: build

&#x20; script:

&#x20;   - echo "Building"

```



```text

build-job

&#x20;├── stage

&#x20;└── script

&#x20;     └── command

```



| Rule | |

|---|---|

| ✅ Use \*\*2 spaces\*\* per level | Common convention |

| ✅ Keep indentation \*\*consistent\*\* | Mixed levels break structure |

| ❌ \*\*Never use tabs\*\* | YAML rejects tab indentation |



> 🔧 Use \*\*Build → Pipeline editor\*\* in GitLab to validate YAML before committing.



\---



\# 🛠️ Hands-On



\## 19. Multi-Stage Pipeline



A pipeline was created with three stages and four jobs:



| Stage | Jobs |

|---|---|

| `build` | `build-job` |

| `test` | `unit-test`, `integration-test` |

| `deploy` | `deploy-job` |



```text

&#x20;                Pipeline

&#x20;                   │

&#x20;                   ▼

&#x20;                BUILD

&#x20;                   │

&#x20;              build-job

&#x20;                   │

&#x20;                   ▼

&#x20;                 TEST

&#x20;             ┌─────┴─────┐

&#x20;             │           │

&#x20;         unit-test   integration-test     ← run in parallel

&#x20;             │           │

&#x20;             └─────┬─────┘

&#x20;                   │

&#x20;                   ▼

&#x20;                DEPLOY

&#x20;                   │

&#x20;               deploy-job

```



\---



\## 20. Complete Lesson 12 Pipeline



The final pipeline combined all concepts from this lesson:



```yaml

\# Application configuration

variables:

&#x20; APP\_NAME: "student-api"

&#x20; ENVIRONMENT: "development"



\# Common configuration

default:

&#x20; before\_script:

&#x20;   - echo "Preparing CI environment"



\# Pipeline execution order

stages:

&#x20; - build

&#x20; - test

&#x20; - deploy



\# Build application

build-job:

&#x20; stage: build



&#x20; script:

&#x20;   - echo "Starting build"

&#x20;   - echo "Application: $APP\_NAME"

&#x20;   - echo "Environment: $ENVIRONMENT"

&#x20;   - echo "Project: $CI\_PROJECT\_NAME"

&#x20;   - echo "Branch: $CI\_COMMIT\_BRANCH"

&#x20;   - echo "Commit: $CI\_COMMIT\_SHORT\_SHA"

&#x20;   - echo "Pipeline ID: $CI\_PIPELINE\_ID"

&#x20;   - echo "Building application"

&#x20;   - echo "Build completed"



&#x20; after\_script:

&#x20;   - echo "Build job cleanup completed"



\# Run unit tests

unit-test:

&#x20; stage: test



&#x20; script:

&#x20;   - echo "Starting unit tests"

&#x20;   - echo "Application: $APP\_NAME"

&#x20;   - echo "Branch: $CI\_COMMIT\_BRANCH"

&#x20;   - echo "Running unit tests"

&#x20;   - echo "Unit tests completed"



&#x20; after\_script:

&#x20;   - echo "Unit test cleanup completed"



\# Run integration tests

integration-test:

&#x20; stage: test



&#x20; script:

&#x20;   - echo "Starting integration tests"

&#x20;   - echo "Application: $APP\_NAME"

&#x20;   - echo "Branch: $CI\_COMMIT\_BRANCH"

&#x20;   - echo "Running integration tests"

&#x20;   - echo "Integration tests completed"



&#x20; after\_script:

&#x20;   - echo "Integration test cleanup completed"



\# Deploy application

deploy-job:

&#x20; stage: deploy



&#x20; script:

&#x20;   - echo "Starting deployment"

&#x20;   - echo "Application: $APP\_NAME"

&#x20;   - echo "Environment: $ENVIRONMENT"

&#x20;   - echo "Pipeline ID: $CI\_PIPELINE\_ID"

&#x20;   - echo "Deploying application"

&#x20;   - echo "Deployment completed"



&#x20; after\_script:

&#x20;   - echo "Deployment cleanup completed"

```



What each job's log shows, in order:



```text

Preparing CI environment        ← default before\_script

<script lines>                  ← job script

<cleanup message>               ← job after\_script

```



\---



\## 21. Pipeline Execution Flow



```text

Git Push

&#x20;  │

&#x20;  ▼

GitLab detects .gitlab-ci.yml

&#x20;  │

&#x20;  ▼

Pipeline Created

&#x20;  │

&#x20;  ▼

Build Stage

&#x20;  └── build-job

&#x20;  │

&#x20;  ▼

Test Stage

&#x20;  ├── unit-test

&#x20;  └── integration-test

&#x20;  │

&#x20;  ▼

Deploy Stage

&#x20;  └── deploy-job

&#x20;  │

&#x20;  ▼

Pipeline Completed ✅

```



The \*\*GitLab Runner\*\* executes each individual job.



\---



\## 22. Common `.gitlab-ci.yml` Mistakes



\### ❌ Mistake 1 — Wrong indentation



Incorrect YAML structure causes configuration errors. Keep indentation consistent and never use tabs.



\### ❌ Mistake 2 — Undefined stage



```yaml

stages:

&#x20; - build

&#x20; - test



deploy-job:

&#x20; stage: deploy   # ❌ 'deploy' is not in stages

```



GitLab rejects the configuration with an error saying the chosen stage does not exist.



\### ❌ Mistake 3 — Missing `script`



```yaml

build-job:

&#x20; stage: build    # ❌ no script

```



A regular job must define `script` (or `trigger`). Without it, GitLab reports an invalid configuration and \*\*no pipeline is created\*\*.



\### ❌ Mistake 4 — Incorrect variable reference



```yaml

\- echo "$APP\_NAME"   # ✅ prints: student-api

\- echo "APP\_NAME"    # ❌ prints the literal text: APP\_NAME

```



\### ❌ Mistake 5 — Hard-coding secrets



```yaml

variables:

&#x20; DB\_PASSWORD: "SuperSecret123"   # ❌ never do this

```



Passwords and API tokens must \*\*not\*\* be committed to the repository.



\---



\## 23. Hands-On Completed



| Concept practiced | Status |

|---|---|

| Multi-stage pipeline | ✅ |

| Multiple jobs | ✅ |

| `stage` | ✅ |

| `script` | ✅ |

| `before\_script` | ✅ |

| `after\_script` | ✅ |

| `default` | ✅ |

| Custom variables | ✅ |

| GitLab predefined variables | ✅ |

| YAML comments | ✅ |

| YAML indentation | ✅ |

| Pipeline/job logs | ✅ |

| GitLab Runner execution | ✅ |



| Item | Value |

|---|---|

| Project | `gitlab-zero-to-production` |

| Branch | `feature/lesson12-yaml-deep-dive` |



\---



\# 📋 Summary



\## 24. Key Takeaways



| Concept | Purpose |

|---|---|

| `.gitlab-ci.yml` | Defines the GitLab CI/CD pipeline |

| `stages` | Defines pipeline execution order |

| Jobs | Individual units of work |

| `stage` | Assigns a job to a stage |

| `script` | Commands executed by a job |

| `before\_script` | Preparation commands (same shell as `script`) |

| `after\_script` | Post-job / cleanup commands (runs even on failure) |

| `default` | Common configuration for all jobs (overridden by job-level settings) |

| `variables` | Reusable configuration values |

| Predefined variables | Pipeline information provided automatically by GitLab |

| YAML | Uses indentation and hierarchy to structure configuration |



\---



\## 25. Lesson 12 Completion Checklist



\- \[x] Understand `.gitlab-ci.yml`

\- \[x] Understand YAML structure

\- \[x] Understand `stages`

\- \[x] Understand jobs

\- \[x] Understand `stage`

\- \[x] Understand `script`

\- \[x] Understand `before\_script`

\- \[x] Understand `after\_script`

\- \[x] Understand `default`

\- \[x] Understand global vs job-level configuration

\- \[x] Understand variables

\- \[x] Understand predefined GitLab CI/CD variables

\- \[x] Understand YAML comments

\- \[x] Understand YAML indentation

\- \[x] Understand common YAML mistakes

\- \[x] Create a multi-stage pipeline

\- \[x] Execute the pipeline using GitLab Runner

\- \[x] Inspect job logs



\---



\### ✅ Status: Lesson 12 — Completed



