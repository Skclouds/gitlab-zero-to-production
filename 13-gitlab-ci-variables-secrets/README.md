# Lesson 13 — GitLab CI/CD Variables & Secrets

## 🎯 Overview

This lesson focuses on managing **configuration values** and **sensitive information** in GitLab CI/CD pipelines.

The objective is to understand how variables are defined, consumed, **protected**, **masked**, **scoped to environments**, and managed at different levels — and why sensitive credentials must **never** be hard-coded in `.gitlab-ci.yml`.

---

## 📚 Table of Contents

**Concepts**

1. [Learning Objectives](#1-learning-objectives)
2. [Why CI/CD Variables Are Required](#2-why-cicd-variables-are-required)
3. [Basic CI/CD Variable](#3-basic-cicd-variable)
4. [Project CI/CD Variables](#4-project-cicd-variables)

**Hands-On: Project Variables**

5. [Creating a Project Variable](#5-hands-on-creating-a-project-variable)
6. [Using a Project Variable in a Job](#6-using-a-project-variable-in-a-job)
7. [GitLab Runner and CI/CD Variables](#7-gitlab-runner-and-cicd-variables)

**Visibility & Protection**

8. [Masked Variables](#8-masked-variables)
9. [Masked and Hidden Variables](#9-masked-and-hidden-variables)
10. [Protected Variables](#10-protected-variables)
11. [Masked vs Protected](#11-masked-vs-protected)
12. [Why Protected Variables Are Important](#12-why-protected-variables-are-important)

**Environments & Groups**

13. [Environment-Scoped Variables](#13-environment-scoped-variables)
14. [GitLab Environments](#14-gitlab-environments)
15. [Environment-Specific Variable Hands-On](#15-environment-specific-variable-hands-on)
16. [Group CI/CD Variables](#16-group-cicd-variables)
17. [Project vs Group Variables](#17-project-vs-group-variables)
18. [GitLab Learning Group](#18-gitlab-learning-group)

**Precedence**

19. [Variable Precedence](#19-variable-precedence)
20. [Hands-On Variable Precedence](#20-hands-on-variable-precedence)

**Security & Summary**

21. [Important Security Rules](#21-important-security-rules)
22. [Production CI/CD Secret Architecture](#22-production-cicd-secret-architecture)
23. [Common Mistakes](#23-common-mistakes)
24. [Hands-On Summary](#24-hands-on-summary)
25. [Key Takeaways](#25-key-takeaways)
26. [Lesson 13 Completion Checklist](#26-lesson-13-completion-checklist)

---

## 1. Learning Objectives

By the end of this lesson, the following concepts were covered:

- GitLab CI/CD variables
- Project-level CI/CD variables
- Group-level CI/CD variables
- Masked variables
- Protected variables
- Environment-scoped variables
- Predefined GitLab CI/CD variables
- Consuming variables inside CI/CD jobs
- Variable precedence
- Configuration values vs secrets
- Basic secret-management practices
- Why secrets must not be committed to Git repositories

---

## 2. Why CI/CD Variables Are Required

Real pipelines need configuration values **and** credentials:

```text
Database host
Database username
Database password
API tokens
AWS credentials
JFrog credentials
Docker registry credentials
SonarQube tokens
Application configuration
Environment information
```

❌ **Hard-coding** sensitive values in `.gitlab-ci.yml` is a bad practice:

```yaml
variables:
  DB_PASSWORD: "mypassword123"   # ❌
```

The value becomes part of the repository and **remains in Git history** — even if you delete it in a later commit. Anyone who can read the repository (or its history) can read the secret.

✅ Sensitive values belong in **GitLab CI/CD variable settings**, outside the code.

---

## 3. Basic CI/CD Variable

A **variable** is a named value reused inside CI/CD jobs (Lesson 12):

```yaml
variables:
  APP_NAME: "student-api"
  ENVIRONMENT: "development"
```

```yaml
script:
  - echo "$APP_NAME"
  - echo "$ENVIRONMENT"
```

Output:

```text
student-api
development
```

> 💡 This is fine for **non-sensitive** configuration. Secrets go in the GitLab UI instead (next section).

---

## 4. Project CI/CD Variables

A **project CI/CD variable** belongs to one GitLab project and is stored **in GitLab, not in the repository**.

```text
Project
   ↓
Settings
   ↓
CI/CD
   ↓
Variables
```

| Field | Example |
|---|---|
| Key | `DEMO_SECRET` |
| Value | `<secret value>` |

Jobs reference it as `$DEMO_SECRET`. The actual value **never appears** in `.gitlab-ci.yml`.

> 🔐 **Analogy:** `.gitlab-ci.yml` is a **recipe** that says "add the secret sauce." GitLab keeps the secret sauce **locked in a safe** and adds it only when the dish is being cooked.

---

# 🛠️ Hands-On: Project Variables

## 5. Hands-On: Creating a Project Variable

| Setting | Value |
|---|---|
| Key | `DEMO_SECRET` |
| Visibility | Masked |
| Value | A **dummy** practice value (not a real credential) |

Purpose: show that a variable stored in GitLab can be consumed by a job **without** storing its value in the repository.

> ⚠️ GitLab only accepts a value for masking if it meets certain rules — for example, it must be a **single line** and at least **8 characters** long, using only allowed characters. If the **Masked** option is rejected, check the value against these rules.

---

## 6. Using a Project Variable in a Job

```yaml
secret-test:
  stage: test
  script:
    - echo "Testing GitLab CI/CD variable"
    - echo "Secret variable is configured"
    - 'echo "Secret value: $DEMO_SECRET"'
```

The pipeline configuration contains only the **reference** `$DEMO_SECRET` — **not** the value. GitLab supplies the value when the job runs.

Expected log output:

```text
Secret value: [MASKED]
```

> ⚠️ Printing a secret is done here **only to demonstrate masking**. Never do this in a real pipeline (see [section 21](#21-important-security-rules)).

---

## 7. GitLab Runner and CI/CD Variables

```text
GitLab Project
      │
      ▼
CI/CD Variable
      │
      ▼
Pipeline
      │
      ▼
GitLab Runner
      │
      ▼
CI Job
      │
      ▼
$DEMO_SECRET
```

The Runner receives the variable as part of the **job's environment**, only for the duration of that job.

> 💡 On a **Shell** Runner (Lesson 14), the job runs directly on your machine — so anyone controlling the job's script can read the variable. That's one reason Runner isolation matters.

---

# 🔒 Visibility & Protection

## 8. Masked Variables

A **masked** variable is hidden in job logs:

```text
Secret value: [MASKED]
```

Purpose: reduce **accidental** exposure of sensitive values through CI/CD logs.

> ⚠️ **Masking is not a guarantee.** The job still has full access to the real value. A script could transform it (e.g. base64-encode it, or print it one character at a time), and the transformed text would **not** be masked. Production pipelines should **never print secrets**, even masked ones.

---

## 9. Masked and Hidden Variables

Newer GitLab versions offer a stronger option:

| Visibility | Hidden in job logs | Value viewable in the GitLab UI after saving |
|---|---|---|
| **Visible** | ❌ | ✅ |
| **Masked** | ✅ | ✅ (to users with access to settings) |
| **Masked and hidden** | ✅ | ❌ — cannot be revealed after creation |

> 💡 With **Masked and hidden**, the value can't be viewed again in the UI — only replaced. Use it for credentials nobody needs to read back.

---

## 10. Protected Variables

A **protected** variable is only passed to pipelines running on **protected branches or protected tags** (Lesson 9).

| Setting | Value |
|---|---|
| Protect variable | ✅ Enabled |

Purpose: keep sensitive variables (like production credentials) away from pipelines on **unprotected** branches.

```text
feature/*            → No production secret ❌
develop (unprotected)→ No production secret ❌
main (protected 🔒)  → Production secret available ✅
```

> 💡 On an unprotected branch, a protected variable is simply **not set** — `$PRODUCTION_API_TOKEN` is empty. Jobs don't error by themselves; they just receive nothing.

---

## 11. Masked vs Protected

These solve **different** problems:

| Setting | Controls | Question it answers |
|---|---|---|
| **Visible** | — | The value can appear in logs |
| **Masked** | **Exposure** in logs | *Can people see the value in job output?* |
| **Masked and hidden** | Exposure in logs **and** UI | *Can anyone read the value back later?* |
| **Protected** | **Availability** | *Which pipelines receive the value at all?* |

A variable can be **both**:

```text
Masked + Protected
```

This is a common pattern for **production credentials**.

---

## 12. Why Protected Variables Are Important

Consider `PRODUCTION_API_TOKEN`. A feature branch should **not** have access to it — otherwise anyone who can push a branch could write a job that uses the production token.

```text
Feature Branch
      ├── Build
      ├── Test
      ├── Quality Checks
      └── ❌ No production credentials

Protected Main 🔒
      ├── Build
      ├── Test
      └── Production Deployment
              └── ✅ Production credentials
```

> 🔗 **Connection to Lessons 8 & 9:** Only Maintainers can merge into protected `main`. So protected variables mean only **reviewed, merged** code can use production secrets.

---

# 🌍 Environments & Groups

## 13. Environment-Scoped Variables

Different environments need different values:

```text
development → dev database
staging     → staging database
production  → production database
```

An **environment-scoped** variable lets the **same variable name** have different values per environment:

| Key | Environment scope | Value |
|---|---|---|
| `DATABASE_HOST` | `development` | `dev-database.example.com` |
| `DATABASE_HOST` | `staging` | `staging-database.example.com` |
| `DATABASE_HOST` | `production` | `prod-database.example.com` |

The default scope `*` (All) makes a variable available to **every** job.

---

## 14. GitLab Environments

A job is linked to an environment with the `environment` keyword:

```yaml
deploy-development:
  stage: deploy
  environment:
    name: development
  script:
    - echo "Deploying to development"
```

```yaml
environment:
  name: development
```

tells GitLab this job deploys to the **development** environment. GitLab also lists it under **Operate → Environments**.

> 💡 An environment-scoped variable is passed **only** to jobs whose `environment: name` matches its scope. A job with no `environment` keyword receives only variables scoped to `*`.

---

## 15. Environment-Specific Variable Hands-On

A practice variable was created:

| Field | Value |
|---|---|
| Key | `DATABASE_HOST` |
| Environment scope | `development` |
| Value | `dev-database.example.com` *(dummy value)* |

Deployment job:

```yaml
deploy-development:
  stage: deploy
  environment:
    name: development
  script:
    - echo "Starting development deployment"
    - 'echo "Database host: $DATABASE_HOST"'
    - echo "Development deployment completed"
```

```text
deploy-development
        ↓
development environment
        ↓
DATABASE_HOST
        ↓
dev-database.example.com
```

> 💡 A host name isn't a secret, so printing it is fine here.

---

## 16. Group CI/CD Variables

**Group variables** are defined at the group level and **inherited** by all projects in that group hierarchy (including subgroups).

```text
GitLab Learning
       │
       ▼
Group CI/CD Variables
       ├── SONAR_HOST_URL
       ├── JFROG_URL
       └── DOCKER_REGISTRY
       │
       ▼
Projects within the group
```

Useful when **many projects share** the same configuration — update it once, every project gets it.

> 🔗 This mirrors **permission inheritance** from Lesson 8.

---

## 17. Project vs Group Variables

| Variable Type | Scope | Examples |
|---|---|---|
| **Project variable** | One project | `APP_NAME` |
| **Group variable** | Group and all child projects | `SONAR_HOST_URL`, `JFROG_URL`, `DOCKER_REGISTRY` |

Project-specific configuration stays at the project level; organization-wide configuration is managed at the group level.

---

## 18. GitLab Learning Group

The **GitLab Learning** group (created in Lesson 8) can demonstrate group-level variables:

| Field | Value |
|---|---|
| Key | `COMMON_TOOL_NAME` |
| Value | `GitLab-DevOps-Tools` |

```text
GitLab Learning
       │
       ▼
Group CI/CD Variable
       │
       ▼
Projects within the group
       │
       ▼
CI/CD Pipeline
       │
       ▼
$COMMON_TOOL_NAME
```

> ⚠️ A project must be **inside** the group's hierarchy for inheritance to apply. In Lesson 8, `gitlab-zero-to-production` was intentionally kept **outside** the GitLab Learning group — so it does **not** receive `$COMMON_TOOL_NAME`. To test group variables, create a small practice project **inside** GitLab Learning (or its DevOps subgroup).

---

# ⚖️ Precedence

## 19. Variable Precedence

**Variable precedence** decides which value wins when the same variable is defined in several places.

GitLab's order, from **highest** to **lowest** priority (simplified):

| Priority | Where the variable is defined |
|---|---|
| 1 (highest) | Pipeline variables — set when running a pipeline manually, by a trigger, or a schedule |
| 2 | **Project** CI/CD variables (Settings → CI/CD → Variables) |
| 3 | **Group** CI/CD variables (closest subgroup wins) |
| 4 | Instance CI/CD variables |
| 5 | **Job-level** `variables:` in `.gitlab-ci.yml` |
| 6 | **Global/default** `variables:` in `.gitlab-ci.yml` |
| 7 (lowest) | Predefined variables |

> ⚠️ **Counter-intuitive:** Variables set in the **GitLab UI** (project/group settings) **override** variables written in `.gitlab-ci.yml` — even job-level ones. The UI settings win because they're treated as deliberate overrides by someone with project access.

Example:

```text
Group:   APP_NAME = group-app
Project: APP_NAME = project-app
Job:     APP_NAME = job-app      (.gitlab-ci.yml)

Result in the job → project-app
```

Understanding precedence is essential when troubleshooting **unexpected values** in pipelines.

---

## 20. Hands-On Variable Precedence

Create a project-level variable:

| Field | Value |
|---|---|
| Key | `PRECEDENCE_TEST` |
| Value | `project-value` |

Define the same variable in a job:

```yaml
precedence-test:
  stage: test
  variables:
    PRECEDENCE_TEST: "job-value"
  script:
    - 'echo "PRECEDENCE_TEST = $PRECEDENCE_TEST"'
```

Expected output:

```text
PRECEDENCE_TEST = project-value
```

The **project variable wins**, because UI-defined project variables have higher precedence than `variables:` in `.gitlab-ci.yml` (see the table in section 19).

Within `.gitlab-ci.yml` itself, the more specific definition does win:

| Defined in `.gitlab-ci.yml` | Result |
|---|---|
| Global `variables: PRECEDENCE_TEST: "global-value"` and job `variables: PRECEDENCE_TEST: "job-value"` | `job-value` ✅ |

> 🧪 **Try it:** Delete the project variable and re-run the pipeline. The output changes to `job-value` — proving the project variable was overriding it.

---

# 🔐 Security & Summary

## 21. Important Security Rules

### ❌ Do not hard-code credentials

```yaml
variables:
  DB_PASSWORD: "real-password"   # ❌
```

### ❌ Do not commit API tokens

Never store these in source code:

```text
API_TOKEN
AWS_SECRET_KEY
JFROG_TOKEN
DATABASE_PASSWORD
```

### ✅ Use GitLab CI/CD variables

Manage sensitive values through **Settings → CI/CD → Variables**.

### ✅ Mask sensitive values

Reduce accidental exposure in logs.

### ✅ Protect production credentials

Restrict them to protected branches/tags — the pipelines that actually need them.

### ❌ Do not print secrets

```yaml
script:
  - echo "$DB_PASSWORD"   # ❌ even if masked
```

✅ Instead, pass the variable **directly** to the command that needs it:

```yaml
script:
  - docker login -u "$REGISTRY_USER" -p "$REGISTRY_PASSWORD" "$REGISTRY_URL"
```

### 🚨 If a secret is ever leaked

**Rotate it immediately** — revoke the old credential and create a new one. Deleting it from the code or logs is not enough (Lesson 10).

---

## 22. Production CI/CD Secret Architecture

```text
                         GitLab
                           │
              ┌────────────┴────────────┐
              │                         │
        Group Variables          Project Variables
              │                         │
              ▼                         ▼
     Common configuration      Project configuration
              │                         │
              └────────────┬────────────┘
                           │
                           ▼
                    CI/CD Pipeline
                           │
                           ▼
                     GitLab Runner
                           │
                           ▼
                          Job
                           │
                ┌──────────┴──────────┐
                ▼                     ▼
          Configuration            Secrets
                              (masked + protected)
```

> 💡 Larger organizations often go one step further and keep secrets in a dedicated **secrets manager** (e.g. HashiCorp Vault, AWS Secrets Manager), which GitLab jobs fetch at runtime.

---

## 23. Common Mistakes

| # | Mistake | Example | Fix |
|---|---|---|---|
| 1 | Hard-coding secrets | `DB_PASSWORD: "password123"` | Use a masked CI/CD variable |
| 2 | Printing secrets | `echo "$DB_PASSWORD"` | Pass the variable directly to the command |
| 3 | Giving production credentials to feature branches | Unprotected production token | Mark it **Protected** |
| 4 | Confusing masked and protected | "It's masked, so it's safe on any branch" | Masked = **exposure**; Protected = **availability** |
| 5 | Unexpected variable overrides | Job-level value "ignored" | Check precedence — a project/group UI variable may be overriding it |
| 6 | Expecting group variables in an outside project | `$COMMON_TOOL_NAME` is empty | The project must be inside the group hierarchy |

---

## 24. Hands-On Summary

| Concept practiced | Status |
|---|---|
| Project CI/CD variables | ✅ |
| Masked variables | ✅ |
| Protected variables | ✅ |
| Environment-scoped variables | ✅ |
| Group CI/CD variables | ✅ |
| Variable consumption in jobs | ✅ |
| Variable precedence | ✅ |
| Secret-management concepts | ✅ |
| GitLab Runner variable usage | ✅ |

---

## 25. Key Takeaways

| Concept | Meaning |
|---|---|
| **Project variable** | Project-specific configuration or secrets, stored in GitLab |
| **Group variable** | Shared configuration inherited by all projects in a group hierarchy |
| **Masked** | Hides the value in job logs (reduces accidental exposure) |
| **Masked and hidden** | Also prevents viewing the value in the UI after saving |
| **Protected** | Passes the value only to pipelines on protected branches/tags |
| **Environment scope** | Different values per environment (`development`, `staging`, `production`) |
| **Variable precedence** | Decides which value wins — UI variables override `.gitlab-ci.yml` variables |

```text
Project  → CI/CD Variable
Group    → Projects (inherited)
```

---

## 26. Lesson 13 Completion Checklist

- [x] Understand CI/CD variables
- [x] Create a project CI/CD variable
- [x] Use a project variable in a pipeline
- [x] Understand masked variables
- [x] Understand masked and hidden variables
- [x] Understand protected variables
- [x] Understand masked vs protected
- [x] Understand environment-scoped variables
- [x] Associate jobs with environments
- [x] Understand group CI/CD variables
- [x] Understand project vs group variables
- [x] Understand variable precedence
- [x] Understand secret-management fundamentals
- [x] Understand why secrets should not be hard-coded
- [x] Understand why secrets should not be printed in logs

---

### ✅ Status: Lesson 13 — Completed
