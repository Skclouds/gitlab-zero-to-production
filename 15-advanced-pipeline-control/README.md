# Lesson 15 — Advanced GitLab Pipeline Control

## 🎯 Overview

In previous lessons, we learned how GitLab CI/CD pipelines work, how `.gitlab-ci.yml` is structured, how variables are used, and how GitLab Runners execute jobs.

In this lesson, we learned how to control **when pipelines and individual jobs should run**.

Main concepts covered:

- `rules`
- Branch-based rules
- Multiple rules
- `when` — `manual`, `always`, `on_success`, `on_failure`
- `allow_failure`
- `workflow: rules`
- Merge Request pipelines
- `CI_PIPELINE_SOURCE`
- Combining `workflow`, `rules`, `when`, and `allow_failure`
- Production-style pipeline control

---

## 📚 Table of Contents

**Concepts**

1. [Why Pipeline Control Is Important](#1-why-pipeline-control-is-important)
2. [Job Rules vs Workflow Rules](#2-job-rules-vs-workflow-rules)
3. [`rules`](#3-rules)
4. [`CI_COMMIT_BRANCH`](#4-ci_commit_branch)

**Hands-On: Rules**

5. [Branch-Based Rules](#5-hands-on--branch-based-rules)
6. [Running a Job on a Feature Branch](#6-running-a-job-on-a-feature-branch)
7. [Multiple Rules](#7-multiple-rules)

**`when` and `allow_failure`**

8. [`when`](#8-when)
9. [`when: manual`](#9-when-manual)
10. [`when: always`](#10-when-always)
11. [`when: on_success`](#11-when-on_success)
12. [`when: on_failure`](#12-when-on_failure)
13. [`allow_failure`](#13-allow_failure)
14. [`allow_failure` vs `when: manual`](#14-allow_failure-vs-when-manual)

**Pipeline-Level Control**

15. [`workflow: rules`](#15-workflow-rules)
16. [Job Rule vs Workflow Rule](#16-job-rule-vs-workflow-rule)
17. [Merge Request Pipelines](#17-merge-request-pipelines)
18. [`CI_PIPELINE_SOURCE`](#18-ci_pipeline_source)
19. [`CI_COMMIT_BRANCH` vs `CI_PIPELINE_SOURCE`](#19-ci_commit_branch-vs-ci_pipeline_source)
20. [Combining `rules` and `when`](#20-combining-rules-and-when)

**Production**

21. [Production-Style Pipeline Control](#21-production-style-pipeline-control)
22. [Production Pipeline Flow](#22-production-pipeline-flow)
23. [Why Manual Production Deployment Is Useful](#23-why-manual-production-deployment-is-useful)

**Summary**

24. [Hands-On Project](#24-hands-on-project)
25. [Troubleshooting Experience](#25-troubleshooting-experience)
26. [Important Concepts Summary](#26-important-concepts-summary)
27. [Key Mental Model](#27-key-mental-model)
28. [Final Lesson 15 Checklist](#28-final-lesson-15-checklist)
29. [Final Takeaway](#29-final-takeaway)

---

## 1. Why Pipeline Control Is Important

In a real project, we **don't** want every job to run for every change.

```text
Developer pushes feature branch
        ↓
Build
        ↓
Test
        ↓
Merge Request
        ↓
Quality checks
        ↓
Merge to main
        ↓
Production deployment
```

Different events need different CI/CD behavior. Pipeline control lets us define:

- When a **pipeline** should be created
- When a **job** should be created
- When a job should **run**
- Which jobs require **manual approval**
- Which failures should **block** the pipeline
- Which **events** should trigger CI/CD

---

## 2. Job Rules vs Workflow Rules

> ⭐ One of the most important concepts in this lesson.

| | Job-level `rules` | `workflow: rules` |
|---|---|---|
| **Question it answers** | Should **this job** be created? | Should **the entire pipeline** be created? |
| **Where it goes** | Inside a job | At the top level, under `workflow:` |
| **If no rule matches** | Job is not created | No pipeline at all |

Job-level:

```yaml
test:
  script:
    - echo "Running tests"
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
```

Workflow:

```yaml
workflow:
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
```

```text
workflow: rules
        ↓
Should the pipeline exist?
        ↓
Pipeline
        ↓
job rules
        ↓
Which jobs should exist?
```

> 🧠 **Key takeaway:** `workflow: rules` controls **pipeline** creation; job-level `rules` control **individual job** creation.

> 🚪 **Analogy:** `workflow: rules` is the **building's front door** — if it's locked, nobody gets in. Job `rules` are the **doors to individual rooms** inside.

---

## 3. `rules`

`rules` let GitLab **conditionally create** jobs.

```yaml
branch-test:
  stage: test
  script:
    - echo "This job is running"
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
```

```text
Is the branch main?
       │
   YES │ NO
    ↓    ↓
  Run   Don't create
```

> 💡 How GitLab evaluates `rules`:
> - Rules are checked **top to bottom**.
> - The **first matching rule wins** — the rest are ignored.
> - If **no rule matches**, the job is **not added** to the pipeline.
> - `when: never` in a rule explicitly excludes the job.

---

## 4. `CI_COMMIT_BRANCH`

A predefined variable containing the **branch** associated with the commit.

Examples:

```text
main
feature/login
feature/lesson15-pipeline-control
```

```yaml
rules:
  - if: '$CI_COMMIT_BRANCH == "main"'   # run when the branch is main
```

> ⚠️ Reminder from Lesson 12: `CI_COMMIT_BRANCH` is **empty** in Merge Request and tag pipelines.

> 💡 Instead of hard-coding `"main"`, you can use the predefined `$CI_DEFAULT_BRANCH`:
> ```yaml
> - if: '$CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH'
> ```

---

# 🛠️ Hands-On: Rules

## 5. Hands-On — Branch-Based Rules

```yaml
stages:
  - test

branch-test:
  stage: test
  script:
    - echo "This job is running"
    - 'echo "Branch: $CI_COMMIT_BRANCH"'
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
```

The exercise was performed on `feature/lesson15-pipeline-control`.

Because `feature/lesson15-pipeline-control` ≠ `main`:

```text
branch-test → Not created ❌
```

This demonstrated that a job-level rule can **prevent a job from being created**.

> 💡 The line `'echo "Branch: $CI_COMMIT_BRANCH"'` is wrapped in single quotes because the colon followed by a space (`: `) would otherwise confuse the YAML parser.

---

## 6. Running a Job on a Feature Branch

The rule was changed to match the current branch:

```yaml
rules:
  - if: '$CI_COMMIT_BRANCH == "feature/lesson15-pipeline-control"'
```

```text
Rule → TRUE
      ↓
Job created
      ↓
Job passed ✅
```

This demonstrated **conditional job execution**.

> 💡 To match **all** feature branches instead of one, use a regex with `=~`:
> ```yaml
> - if: '$CI_COMMIT_BRANCH =~ /^feature\//'
> ```

---

## 7. Multiple Rules

```yaml
rules:
  - if: '$CI_COMMIT_BRANCH == "main"'
  - if: '$CI_COMMIT_BRANCH == "feature/lesson15-pipeline-control"'
```

GitLab evaluates rules **in order**:

```text
Is branch main?
     │
   YES → Run
     │
    NO
     ↓
Is branch feature/lesson15-pipeline-control?
     │
   YES → Run
     │
    NO
     ↓
No matching rule → job not created
```

Multiple rules let a job support **multiple conditions** (logical OR).

---

# ⏱️ `when` and `allow_failure`

## 8. `when`

The `when` keyword controls **how and when** a job runs.

| Value | Meaning |
|---|---|
| `on_success` | Run if all earlier stages succeeded *(default)* |
| `on_failure` | Run only if an earlier job failed |
| `always` | Run regardless of earlier results |
| `manual` | Wait for a human to click **Play** |
| `never` | Don't run (used inside `rules`) |

---

## 9. `when: manual`

`when: manual` makes a job **wait for human action**.

```yaml
deploy:
  stage: deploy
  script:
    - echo "Deploying application"
  when: manual
```

The job is created but shows a ▶️ **Play** button.

```text
Build
  ↓
Test
  ↓
Deploy (manual)
  ↓
User clicks Play ▶️
  ↓
Deployment
```

Commonly used for **deployment** workflows.

---

## 10. `when: always`

`when: always` runs a job **regardless** of whether earlier jobs passed or failed.

```yaml
pipeline-report:
  stage: report
  script:
    - echo "Pipeline completed"
  when: always
```

Useful for:

- Reports
- Cleanup
- Notifications
- Log collection

```text
Previous job
      ├── Passed ─┐
      └── Failed ─┤
                  ↓
           when: always
                  ↓
            Run the job
```

> ⚠️ Remember: the `report` stage must be listed under `stages:` — otherwise GitLab reports a "stage does not exist" error (Lesson 12).

---

## 11. `when: on_success`

`on_success` (the **default**) runs a job only when all jobs in earlier stages have **succeeded**.

```yaml
deploy:
  stage: deploy
  script:
    - echo "Deploying"
  when: on_success
```

```text
Build → ✅ → Test → ✅ → Deploy
```

This is the normal successful pipeline flow.

---

## 12. `when: on_failure`

`on_failure` runs a job **only when at least one job in an earlier stage failed**.

```yaml
failure-report:
  stage: report
  script:
    - echo "A previous job failed"
  when: on_failure
```

Useful for failure handling, alerts, and diagnostics.

| Earlier stages | `on_success` job | `on_failure` job | `always` job |
|---|---|---|---|
| ✅ All passed | Runs | Skipped | Runs |
| ❌ Something failed | Skipped | Runs | Runs |

---

## 13. `allow_failure`

`allow_failure` controls whether a job's failure **blocks the pipeline**.

```yaml
quality-check:
  stage: quality
  script:
    - echo "Running quality check"
    - exit 1
  allow_failure: true
```

```text
quality-check → Failed ⚠️ (allowed)
Pipeline      → Passed with warnings
```

The job shows an **orange warning** icon, later stages still run, and the pipeline does not fail.

> 💡 Useful for **non-blocking** checks — e.g. a new linter the team is still adopting.

---

## 14. `allow_failure` vs `when: manual`

These solve **different problems**:

| Feature | Purpose | Example meaning |
|---|---|---|
| `when: manual` | Wait for human action | "Wait for someone to start this job." |
| `allow_failure: true` | Failure doesn't block the pipeline | "Run this job, but its failure is acceptable." |

```yaml
deploy:
  when: manual          # waits for a click
```

```yaml
quality-check:
  allow_failure: true   # runs automatically; failure is tolerated
```

---

# 🚦 Pipeline-Level Control

## 15. `workflow: rules`

`workflow: rules` controls whether the **complete pipeline** is created.

```yaml
workflow:
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
```

```text
Push to main           → Pipeline created ✅
Push to another branch → Pipeline not created ❌
```

---

## 16. Job Rule vs Workflow Rule

**Job rule:**

```yaml
test:
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
```

```text
Pipeline exists
       ↓
Test job condition evaluated
       ↓
Job may or may not be created
```

**Workflow rule:**

```yaml
workflow:
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
```

```text
Condition evaluated
       ↓
Pipeline may or may not be created
```

> 🧠 `workflow: rules` is evaluated at the **pipeline** level; job `rules` operate at the **job** level.

---

## 17. Merge Request Pipelines

A **Merge Request pipeline** runs CI/CD checks when code is proposed for merging.

```text
Feature branch
      ↓
Merge Request
      ↓
CI/CD Pipeline
      ├── Build
      ├── Test
      ├── Quality
      └── Security
      ↓
Passed ✅
      ↓
Merge
```

Teams can validate changes **before** merging them into the target branch.

---

## 18. `CI_PIPELINE_SOURCE`

`CI_PIPELINE_SOURCE` indicates **what triggered** the pipeline.

```yaml
workflow:
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
```

This means: create the pipeline when it's associated with a **Merge Request** event.

Common values:

| Value | Triggered by |
|---|---|
| `push` | A `git push` |
| `merge_request_event` | Creating or updating a Merge Request |
| `web` | **Run pipeline** button in the UI |
| `schedule` | A pipeline schedule |
| `api` | The pipelines API |
| `trigger` | A trigger token |

---

## 19. `CI_COMMIT_BRANCH` vs `CI_PIPELINE_SOURCE`

| Variable | Question it answers | Example values |
|---|---|---|
| `CI_COMMIT_BRANCH` | **Which branch** is associated with the commit? | `main`, `feature/login` |
| `CI_PIPELINE_SOURCE` | **What caused** the pipeline to run? | `push`, `merge_request_event`, `web`, `schedule` |

This distinction is important when designing production pipelines.

---

## 20. Combining `rules` and `when`

`when` can be set **inside** a rule:

```yaml
production-deploy:
  stage: deploy
  script:
    - echo "Production deployment"
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
      when: manual
```

> If the branch is `main`, create the deployment job — but require a **human** to trigger it.

```text
Feature branch
      ↓
No production deployment job

main
 ↓
Production deployment job
 ↓
Manual
 ↓
Human clicks Play ▶️
 ↓
Deployment
```

> ⚠️ **Subtle difference:** When `when: manual` is set **inside `rules`**, `allow_failure` defaults to `false`. The pipeline then shows as **blocked** ⏸️ until someone runs the job. When `when: manual` is set at the **job level** (outside `rules`), `allow_failure` defaults to `true` and the pipeline can complete without it.

---

# 🏭 Production

## 21. Production-Style Pipeline Control

A production pipeline combining everything:

```yaml
workflow:
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
    - if: '$CI_COMMIT_BRANCH == "main"'

stages:
  - test
  - quality
  - deploy

test:
  stage: test
  script:
    - echo "Running tests"
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
    - if: '$CI_COMMIT_BRANCH == "main"'

quality-check:
  stage: quality
  script:
    - echo "Running quality checks"
  allow_failure: true
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event"'
    - if: '$CI_COMMIT_BRANCH == "main"'

production-deploy:
  stage: deploy
  script:
    - echo "Production deployment started"
    - echo "Production deployment completed"
  rules:
    - if: '$CI_COMMIT_BRANCH == "main"'
      when: manual
```

> 💡 This workflow creates pipelines **only** for MRs and `main`. A plain push to a feature branch without an MR creates **no pipeline** — which also prevents **duplicate pipelines** (one for the push and one for the MR).

---

## 22. Production Pipeline Flow

**Merge Request:**

```text
Merge Request
      ↓
workflow rule matches
      ↓
Pipeline created
      ↓
Test
      ↓
Quality Check
      ↓
No production deployment
```

**Main branch:**

```text
main
 ↓
workflow rule matches
 ↓
Pipeline created
 ↓
Test
 ↓
Quality Check
 ↓
Production Deployment
 ↓
Manual approval ▶️
 ↓
Deployment
```

---

## 23. Why Manual Production Deployment Is Useful

Production deployments often need **additional controls**:

```text
Code
 ↓
Build
 ↓
Unit Tests
 ↓
SonarQube
 ↓
Security Scan
 ↓
Artifact
 ↓
Manual Approval ▶️
 ↓
Production
```

This reduces the chance of an **unintended** production deployment.

Later in this roadmap, these concepts will be combined with:

- SonarQube
- JFrog Artifactory
- Docker
- Terraform
- Kubernetes

---

# 📋 Summary

## 24. Hands-On Project

Branch: `feature/lesson15-pipeline-control`

Exercises demonstrated:

1. Branch-based rules
2. Multiple rules
3. Manual jobs
4. Allowed failures
5. Workflow rules
6. Merge Request pipelines
7. `CI_PIPELINE_SOURCE`
8. `when: always`
9. `when: manual`
10. Combining workflow and job rules

---

## 25. Troubleshooting Experience

Pipeline behavior was intentionally changed to see how GitLab evaluates rules:

| What didn't match | Result | Level |
|---|---|---|
| Job-level `rules` | Job → **Not created** | Job control |
| `workflow: rules` | Pipeline → **Not created** | Pipeline control |

> 🔧 **Debugging tip:** If a pipeline doesn't appear at all, check `workflow: rules` first. If the pipeline appears but a job is missing, check that job's `rules`.

---

## 26. Important Concepts Summary

| Concept | Meaning |
|---|---|
| `rules` | Controls job creation/execution conditions (first match wins) |
| `workflow: rules` | Controls whether a pipeline is created |
| `CI_COMMIT_BRANCH` | Branch of the commit (empty in MR/tag pipelines) |
| `CI_PIPELINE_SOURCE` | What triggered the pipeline |
| `when: manual` | Requires manual action |
| `when: always` | Runs regardless of previous results |
| `when: on_success` | Runs after successful previous work (default) |
| `when: on_failure` | Runs when previous work fails |
| `allow_failure` | Allows a job failure without blocking the pipeline |

---

## 27. Key Mental Model

```text
                 GitLab Event
                      │
                      ↓
              workflow: rules
           Should pipeline exist?
                      │
                      ↓
                  Pipeline
                      │
                      ↓
               Job-level rules
             Should job exist?
                      │
                      ↓
                     Job
                      │
                      ↓
                    when
           How should it behave?
                      │
                      ↓
                GitLab Runner
                      │
                      ↓
                Job execution
```

---

## 28. Final Lesson 15 Checklist

- [x] Understand pipeline control
- [x] Understand job-level rules
- [x] Understand branch-based rules
- [x] Understand multiple rules
- [x] Understand `when`
- [x] Understand `when: manual`
- [x] Understand `when: always`
- [x] Understand `when: on_success`
- [x] Understand `when: on_failure`
- [x] Understand `allow_failure`
- [x] Understand `workflow: rules`
- [x] Understand pipeline-level control
- [x] Understand `CI_PIPELINE_SOURCE`
- [x] Understand Merge Request pipelines
- [x] Combine `rules` with `when`
- [x] Design a production-style pipeline flow

---

## 29. Final Takeaway

> 🧠 **`workflow: rules` decides whether the pipeline exists, job `rules` decide which jobs exist, and `when` controls how an eligible job behaves.**

```text
Event
  ↓
workflow: rules → Pipeline?
  ↓
Job rules       → Job?
  ↓
when            → How should it run?
  ↓
Runner
  ↓
Execution
```

This is the foundation for designing **production-grade** GitLab CI/CD pipelines.

---

### ✅ Status: Lesson 15 — Completed
