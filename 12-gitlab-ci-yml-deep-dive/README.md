# Lesson 12 — `.gitlab-ci.yml` Deep Dive

## 🎯 Overview

This lesson focuses on the **structure, syntax, components, and commonly used keywords** of the GitLab CI/CD configuration file:

```text
.gitlab-ci.yml
```

The objective is to understand how GitLab CI/CD pipelines are defined using **YAML**, and how different configuration elements work together to control pipeline execution.

---

## 📚 Table of Contents

**Concepts**

1. [Learning Objectives](#1-learning-objectives)
2. [What Is `.gitlab-ci.yml`?](#2-what-is-gitlab-ciyml)
3. [Basic Pipeline Structure](#3-basic-pipeline-structure)
4. [`stages`](#4-stages)
5. [Jobs](#5-jobs)
6. [`stage`](#6-stage)
7. [`script`](#7-script)
8. [`before_script`](#8-before_script)
9. [`after_script`](#9-after_script)
10. [`default`](#10-default)
11. [Global vs Job-Level Configuration](#11-global-vs-job-level-configuration)
12. [Variables](#12-variables)
13. [Why Variables Are Useful](#13-why-variables-are-useful)
14. [Configuration Variables vs Secrets](#14-configuration-variables-vs-secrets)
15. [GitLab Predefined CI/CD Variables](#15-gitlab-predefined-cicd-variables)
16. [Custom Variables vs Predefined Variables](#16-custom-variables-vs-predefined-variables)
17. [YAML Comments](#17-yaml-comments)
18. [YAML Indentation](#18-yaml-indentation)

**Hands-On**

19. [Multi-Stage Pipeline](#19-multi-stage-pipeline)
20. [Complete Lesson 12 Pipeline](#20-complete-lesson-12-pipeline)
21. [Pipeline Execution Flow](#21-pipeline-execution-flow)
22. [Common `.gitlab-ci.yml` Mistakes](#22-common-gitlab-ciyml-mistakes)
23. [Hands-On Completed](#23-hands-on-completed)

**Summary**

24. [Key Takeaways](#24-key-takeaways)
25. [Lesson 12 Completion Checklist](#25-lesson-12-completion-checklist)

---

## 1. Learning Objectives

By the end of this lesson, the following concepts were covered:

- YAML fundamentals used in GitLab CI/CD
- The structure of `.gitlab-ci.yml`
- `stages`, jobs, and `stage`
- `script`, `before_script`, and `after_script`
- `default`
- Global vs job-level configuration
- Custom CI/CD variables
- Predefined GitLab CI/CD variables
- YAML comments and indentation
- Common `.gitlab-ci.yml` mistakes
- Building and executing a **multi-stage pipeline**

---

## 2. What Is `.gitlab-ci.yml`?

`.gitlab-ci.yml` is the configuration file GitLab CI/CD uses to define **how a pipeline should execute**.

It contains:

- Pipeline stages
- Jobs
- Commands to execute
- Variables
- Common job configuration
- Job-specific configuration

The file must be placed in the **root directory** of the repository:

```text
gitlab-zero-to-production/
│
├── .gitlab-ci.yml   ← here
├── README.md
└── application files
```

GitLab detects `.gitlab-ci.yml` and uses it to create and run pipelines.

> 🍳 **Analogy:** `.gitlab-ci.yml` is a **recipe**. Stages are the courses (starter → main → dessert), jobs are the dishes, and `script` lines are the cooking steps. The Runner is the chef who follows them.

---

## 3. Basic Pipeline Structure

```yaml
stages:
  - build
  - test

build-job:
  stage: build
  script:
    - echo "Building application"

test-job:
  stage: test
  script:
    - echo "Testing application"
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
    ├── build-job
    └── test-job
```

---

## 4. `stages`

The `stages` keyword defines the **execution order** of stages.

```yaml
stages:
  - build
  - test
  - deploy
```

```text
build
  ↓
test
  ↓
deploy
```

Example with jobs:

```yaml
stages:
  - build
  - test

build-job:
  stage: build
  script:
    - echo "Build"

unit-test:
  stage: test
  script:
    - echo "Unit Test"
```

The `test` stage starts **only after** the `build` stage completes successfully.

> 💡 If you don't define `stages` at all, GitLab uses the defaults: `.pre → build → test → deploy → .post`. A job without a `stage` keyword goes to `test`.

---

## 5. Jobs

A **job** is a unit of work executed by GitLab CI/CD.

```yaml
build-job:
  stage: build
  script:
    - echo "Building application"
```

Here, `build-job` is the **job name**. Any top-level key that isn't a reserved keyword (like `stages`, `variables`, `default`) is treated as a job.

A pipeline can contain many jobs:

```yaml
build-job:
  stage: build
  script:
    - echo "Building"

unit-test:
  stage: test
  script:
    - echo "Running unit tests"

integration-test:
  stage: test
  script:
    - echo "Running integration tests"

deploy-job:
  stage: deploy
  script:
    - echo "Deploying"
```

---

## 6. `stage`

The `stage` keyword (singular) **assigns a job to a stage**.

```yaml
stages:
  - build
  - test
  - deploy

build-job:
  stage: build
```

`build-job` belongs to the `build` stage.

| Keyword | Where | Purpose |
|---|---|---|
| `stages` (plural) | Top level | Defines the list and order of stages |
| `stage` (singular) | Inside a job | Assigns that job to one stage |

---

## 7. `script`

The `script` keyword defines the **commands a job executes**. They run in order, on the GitLab Runner.

```yaml
build-job:
  stage: build
  script:
    - echo "Starting build"
    - echo "Building application"
    - echo "Build completed"
```

In real projects, the commands depend on the technology:

```yaml
# Node.js
script:
  - npm install
  - npm test
  - npm run build
```

```yaml
# Java / Maven
script:
  - mvn clean test
  - mvn package
```

```yaml
# Python
script:
  - python -m pytest
```

> ⚠️ If any command fails (non-zero exit code), the job **stops and fails** immediately.

---

## 8. `before_script`

`before_script` defines commands that run **before** the main `script`.

```yaml
build-job:
  stage: build

  before_script:
    - echo "Preparing build environment"

  script:
    - echo "Building application"
```

```text
before_script
      ↓
script
```

Useful for common preparation: installing dependencies, logging in to a registry, setting up tools.

> 💡 `before_script` and `script` run in the **same shell**. If `before_script` fails, `script` never runs and the job fails.

---

## 9. `after_script`

`after_script` defines commands that run **after** the main `script`.

```yaml
build-job:
  stage: build

  script:
    - echo "Building application"

  after_script:
    - echo "Build cleanup completed"
```

```text
before_script
      ↓
script
      ↓
after_script
```

Useful for cleanup or post-job activities.

> 💡 Key behaviors of `after_script`:
> - It runs **even if `script` fails** — that's what makes it good for cleanup.
> - It runs in a **separate shell**, so variables exported in `script` are not available.
> - A failure in `after_script` does **not** change the job's status.

---

## 10. `default`

The `default` keyword defines **common configuration** inherited by all jobs.

```yaml
default:
  before_script:
    - echo "Preparing CI environment"

build-job:
  stage: build
  script:
    - echo "Building"

test-job:
  stage: test
  script:
    - echo "Testing"
```

Both `build-job` and `test-job` run `echo "Preparing CI environment"` first — without repeating it in each job.

---

## 11. Global vs Job-Level Configuration

| | Global (`default`) | Job-level |
|---|---|---|
| **Where** | Under `default:` | Inside a specific job |
| **Applies to** | All jobs | Only that job |
| **Purpose** | Common behavior | Specific behavior |

Global:

```yaml
default:
  before_script:
    - echo "Preparing CI environment"
```

Job-level:

```yaml
deploy-job:
  stage: deploy

  before_script:
    - echo "Preparing deployment environment"

  script:
    - echo "Deploying application"
```

> ⚠️ **Important:** A job-level setting **replaces** the default — it does **not** add to it. In the example above, `deploy-job` runs only `"Preparing deployment environment"`; the default `"Preparing CI environment"` is skipped for that job.

```text
default before_script   →  used by jobs that don't define their own
job before_script       →  overrides default for that job only
```

---

## 12. Variables

**Variables** are named values reused throughout the pipeline.

```yaml
variables:
  APP_NAME: "student-api"
  ENVIRONMENT: "development"
```

Referenced with `$`:

```yaml
script:
  - echo "Application: $APP_NAME"
  - echo "Environment: $ENVIRONMENT"
```

Output:

```text
Application: student-api
Environment: development
```

> 💡 Variables can also be defined **inside a job**; a job-level variable overrides a global one with the same name for that job.

---

## 13. Why Variables Are Useful

❌ Without variables — the value is repeated:

```yaml
script:
  - echo "Deploying student-api"
  - echo "student-api deployment completed"
```

✅ With a variable — change it once, it updates everywhere:

```yaml
variables:
  APP_NAME: "student-api"

script:
  - echo "Deploying $APP_NAME"
  - echo "$APP_NAME deployment completed"
```

---

## 14. Configuration Variables vs Secrets

Not every variable is a secret.

| Type | Examples | Where to store |
|---|---|---|
| **Configuration** (not sensitive) | `APP_NAME`, `ENVIRONMENT` | ✅ `.gitlab-ci.yml` is fine |
| **Secrets** (sensitive) | Passwords, API tokens, access keys, private credentials | ❌ **Never** in `.gitlab-ci.yml` |

> 🔐 Secrets belong in **Settings → CI/CD → Variables** as **protected / masked** variables — covered in a later lesson.

---

## 15. GitLab Predefined CI/CD Variables

GitLab **automatically** provides variables describing the current pipeline, project, job, commit, and branch.

| Variable | Contains |
|---|---|
| `CI_PROJECT_NAME` | Project name |
| `CI_PROJECT_PATH` | Namespace + project (e.g. `user/project`) |
| `CI_COMMIT_BRANCH` | Branch name (branch pipelines only) |
| `CI_COMMIT_REF_NAME` | Branch **or** tag name |
| `CI_COMMIT_SHA` | Full commit SHA |
| `CI_COMMIT_SHORT_SHA` | First 8 characters of the SHA |
| `CI_COMMIT_MESSAGE` | Commit message |
| `CI_PIPELINE_ID` | Pipeline ID |
| `CI_JOB_ID` | Job ID |
| `CI_JOB_NAME` | Job name |

Example:

```yaml
script:
  - echo "Project: $CI_PROJECT_NAME"
  - echo "Branch: $CI_COMMIT_BRANCH"
  - echo "Commit: $CI_COMMIT_SHORT_SHA"
  - echo "Pipeline ID: $CI_PIPELINE_ID"
```

> ⚠️ `CI_COMMIT_BRANCH` is **empty** in Merge Request pipelines and tag pipelines. Use `CI_COMMIT_REF_NAME` when you need a value that's always set.

---

## 16. Custom Variables vs Predefined Variables

| | Custom Variables | Predefined Variables |
|---|---|---|
| **Defined by** | You (in `.gitlab-ci.yml` or settings) | GitLab, automatically |
| **Example** | `$APP_NAME`, `$ENVIRONMENT` | `$CI_PROJECT_NAME`, `$CI_PIPELINE_ID` |
| **Naming** | Your choice | Usually start with `CI_` or `GITLAB_` |

> 💡 Avoid naming your own variables with the `CI_` prefix to prevent clashes.

---

## 17. YAML Comments

Comments start with `#` and are **ignored** during execution:

```yaml
# Define pipeline stages
stages:
  - build
  - test
  - deploy
```

They document the pipeline for other developers and DevOps engineers.

---

## 18. YAML Indentation

YAML uses **indentation** to represent structure:

```yaml
build-job:
  stage: build
  script:
    - echo "Building"
```

```text
build-job
 ├── stage
 └── script
      └── command
```

| Rule | |
|---|---|
| ✅ Use **2 spaces** per level | Common convention |
| ✅ Keep indentation **consistent** | Mixed levels break structure |
| ❌ **Never use tabs** | YAML rejects tab indentation |

> 🔧 Use **Build → Pipeline editor** in GitLab to validate YAML before committing.

---

# 🛠️ Hands-On

## 19. Multi-Stage Pipeline

A pipeline was created with three stages and four jobs:

| Stage | Jobs |
|---|---|
| `build` | `build-job` |
| `test` | `unit-test`, `integration-test` |
| `deploy` | `deploy-job` |

```text
                 Pipeline
                    │
                    ▼
                 BUILD
                    │
               build-job
                    │
                    ▼
                  TEST
              ┌─────┴─────┐
              │           │
          unit-test   integration-test     ← run in parallel
              │           │
              └─────┬─────┘
                    │
                    ▼
                 DEPLOY
                    │
                deploy-job
```

---

## 20. Complete Lesson 12 Pipeline

The final pipeline combined all concepts from this lesson:

```yaml
# Application configuration
variables:
  APP_NAME: "student-api"
  ENVIRONMENT: "development"

# Common configuration
default:
  before_script:
    - echo "Preparing CI environment"

# Pipeline execution order
stages:
  - build
  - test
  - deploy

# Build application
build-job:
  stage: build

  script:
    - echo "Starting build"
    - echo "Application: $APP_NAME"
    - echo "Environment: $ENVIRONMENT"
    - echo "Project: $CI_PROJECT_NAME"
    - echo "Branch: $CI_COMMIT_BRANCH"
    - echo "Commit: $CI_COMMIT_SHORT_SHA"
    - echo "Pipeline ID: $CI_PIPELINE_ID"
    - echo "Building application"
    - echo "Build completed"

  after_script:
    - echo "Build job cleanup completed"

# Run unit tests
unit-test:
  stage: test

  script:
    - echo "Starting unit tests"
    - echo "Application: $APP_NAME"
    - echo "Branch: $CI_COMMIT_BRANCH"
    - echo "Running unit tests"
    - echo "Unit tests completed"

  after_script:
    - echo "Unit test cleanup completed"

# Run integration tests
integration-test:
  stage: test

  script:
    - echo "Starting integration tests"
    - echo "Application: $APP_NAME"
    - echo "Branch: $CI_COMMIT_BRANCH"
    - echo "Running integration tests"
    - echo "Integration tests completed"

  after_script:
    - echo "Integration test cleanup completed"

# Deploy application
deploy-job:
  stage: deploy

  script:
    - echo "Starting deployment"
    - echo "Application: $APP_NAME"
    - echo "Environment: $ENVIRONMENT"
    - echo "Pipeline ID: $CI_PIPELINE_ID"
    - echo "Deploying application"
    - echo "Deployment completed"

  after_script:
    - echo "Deployment cleanup completed"
```

What each job's log shows, in order:

```text
Preparing CI environment        ← default before_script
<script lines>                  ← job script
<cleanup message>               ← job after_script
```

---

## 21. Pipeline Execution Flow

```text
Git Push
   │
   ▼
GitLab detects .gitlab-ci.yml
   │
   ▼
Pipeline Created
   │
   ▼
Build Stage
   └── build-job
   │
   ▼
Test Stage
   ├── unit-test
   └── integration-test
   │
   ▼
Deploy Stage
   └── deploy-job
   │
   ▼
Pipeline Completed ✅
```

The **GitLab Runner** executes each individual job.

---

## 22. Common `.gitlab-ci.yml` Mistakes

### ❌ Mistake 1 — Wrong indentation

Incorrect YAML structure causes configuration errors. Keep indentation consistent and never use tabs.

### ❌ Mistake 2 — Undefined stage

```yaml
stages:
  - build
  - test

deploy-job:
  stage: deploy   # ❌ 'deploy' is not in stages
```

GitLab rejects the configuration with an error saying the chosen stage does not exist.

### ❌ Mistake 3 — Missing `script`

```yaml
build-job:
  stage: build    # ❌ no script
```

A regular job must define `script` (or `trigger`). Without it, GitLab reports an invalid configuration and **no pipeline is created**.

### ❌ Mistake 4 — Incorrect variable reference

```yaml
- echo "$APP_NAME"   # ✅ prints: student-api
- echo "APP_NAME"    # ❌ prints the literal text: APP_NAME
```

### ❌ Mistake 5 — Hard-coding secrets

```yaml
variables:
  DB_PASSWORD: "SuperSecret123"   # ❌ never do this
```

Passwords and API tokens must **not** be committed to the repository.

---

## 23. Hands-On Completed

| Concept practiced | Status |
|---|---|
| Multi-stage pipeline | ✅ |
| Multiple jobs | ✅ |
| `stage` | ✅ |
| `script` | ✅ |
| `before_script` | ✅ |
| `after_script` | ✅ |
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

---

# 📋 Summary

## 24. Key Takeaways

| Concept | Purpose |
|---|---|
| `.gitlab-ci.yml` | Defines the GitLab CI/CD pipeline |
| `stages` | Defines pipeline execution order |
| Jobs | Individual units of work |
| `stage` | Assigns a job to a stage |
| `script` | Commands executed by a job |
| `before_script` | Preparation commands (same shell as `script`) |
| `after_script` | Post-job / cleanup commands (runs even on failure) |
| `default` | Common configuration for all jobs (overridden by job-level settings) |
| `variables` | Reusable configuration values |
| Predefined variables | Pipeline information provided automatically by GitLab |
| YAML | Uses indentation and hierarchy to structure configuration |

---

## 25. Lesson 12 Completion Checklist

- [x] Understand `.gitlab-ci.yml`
- [x] Understand YAML structure
- [x] Understand `stages`
- [x] Understand jobs
- [x] Understand `stage`
- [x] Understand `script`
- [x] Understand `before_script`
- [x] Understand `after_script`
- [x] Understand `default`
- [x] Understand global vs job-level configuration
- [x] Understand variables
- [x] Understand predefined GitLab CI/CD variables
- [x] Understand YAML comments
- [x] Understand YAML indentation
- [x] Understand common YAML mistakes
- [x] Create a multi-stage pipeline
- [x] Execute the pipeline using GitLab Runner
- [x] Inspect job logs

---

### ✅ Status: Lesson 12 — Completed

---

⬅️ **Previous:** Lesson 11 — GitLab CI/CD Fundamentals | ➡️ **Next:** Lesson 13
