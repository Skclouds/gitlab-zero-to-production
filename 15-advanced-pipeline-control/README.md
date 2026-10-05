\# Lesson 15 — Advanced GitLab Pipeline Control



\## 🎯 Overview



In previous lessons, we learned how GitLab CI/CD pipelines work, how `.gitlab-ci.yml` is structured, how variables are used, and how GitLab Runners execute jobs.



In this lesson, we learned how to control \*\*when pipelines and individual jobs should run\*\*.



Main concepts covered:



\- `rules`

\- Branch-based rules

\- Multiple rules

\- `when` — `manual`, `always`, `on\_success`, `on\_failure`

\- `allow\_failure`

\- `workflow: rules`

\- Merge Request pipelines

\- `CI\_PIPELINE\_SOURCE`

\- Combining `workflow`, `rules`, `when`, and `allow\_failure`

\- Production-style pipeline control



\---



\## 📚 Table of Contents



\*\*Concepts\*\*



1\. \[Why Pipeline Control Is Important](#1-why-pipeline-control-is-important)

2\. \[Job Rules vs Workflow Rules](#2-job-rules-vs-workflow-rules)

3\. \[`rules`](#3-rules)

4\. \[`CI\_COMMIT\_BRANCH`](#4-ci\_commit\_branch)



\*\*Hands-On: Rules\*\*



5\. \[Branch-Based Rules](#5-hands-on--branch-based-rules)

6\. \[Running a Job on a Feature Branch](#6-running-a-job-on-a-feature-branch)

7\. \[Multiple Rules](#7-multiple-rules)



\*\*`when` and `allow\_failure`\*\*



8\. \[`when`](#8-when)

9\. \[`when: manual`](#9-when-manual)

10\. \[`when: always`](#10-when-always)

11\. \[`when: on\_success`](#11-when-on\_success)

12\. \[`when: on\_failure`](#12-when-on\_failure)

13\. \[`allow\_failure`](#13-allow\_failure)

14\. \[`allow\_failure` vs `when: manual`](#14-allow\_failure-vs-when-manual)



\*\*Pipeline-Level Control\*\*



15\. \[`workflow: rules`](#15-workflow-rules)

16\. \[Job Rule vs Workflow Rule](#16-job-rule-vs-workflow-rule)

17\. \[Merge Request Pipelines](#17-merge-request-pipelines)

18\. \[`CI\_PIPELINE\_SOURCE`](#18-ci\_pipeline\_source)

19\. \[`CI\_COMMIT\_BRANCH` vs `CI\_PIPELINE\_SOURCE`](#19-ci\_commit\_branch-vs-ci\_pipeline\_source)

20\. \[Combining `rules` and `when`](#20-combining-rules-and-when)



\*\*Production\*\*



21\. \[Production-Style Pipeline Control](#21-production-style-pipeline-control)

22\. \[Production Pipeline Flow](#22-production-pipeline-flow)

23\. \[Why Manual Production Deployment Is Useful](#23-why-manual-production-deployment-is-useful)



\*\*Summary\*\*



24\. \[Hands-On Project](#24-hands-on-project)

25\. \[Troubleshooting Experience](#25-troubleshooting-experience)

26\. \[Important Concepts Summary](#26-important-concepts-summary)

27\. \[Key Mental Model](#27-key-mental-model)

28\. \[Final Lesson 15 Checklist](#28-final-lesson-15-checklist)

29\. \[Final Takeaway](#29-final-takeaway)



\---



\## 1. Why Pipeline Control Is Important



In a real project, we \*\*don't\*\* want every job to run for every change.



```text

Developer pushes feature branch

&#x20;       ↓

Build

&#x20;       ↓

Test

&#x20;       ↓

Merge Request

&#x20;       ↓

Quality checks

&#x20;       ↓

Merge to main

&#x20;       ↓

Production deployment

```



Different events need different CI/CD behavior. Pipeline control lets us define:



\- When a \*\*pipeline\*\* should be created

\- When a \*\*job\*\* should be created

\- When a job should \*\*run\*\*

\- Which jobs require \*\*manual approval\*\*

\- Which failures should \*\*block\*\* the pipeline

\- Which \*\*events\*\* should trigger CI/CD



\---



\## 2. Job Rules vs Workflow Rules



> ⭐ One of the most important concepts in this lesson.



| | Job-level `rules` | `workflow: rules` |

|---|---|---|

| \*\*Question it answers\*\* | Should \*\*this job\*\* be created? | Should \*\*the entire pipeline\*\* be created? |

| \*\*Where it goes\*\* | Inside a job | At the top level, under `workflow:` |

| \*\*If no rule matches\*\* | Job is not created | No pipeline at all |



Job-level:



```yaml

test:

&#x20; script:

&#x20;   - echo "Running tests"

&#x20; rules:

&#x20;   - if: '$CI\_COMMIT\_BRANCH == "main"'

```



Workflow:



```yaml

workflow:

&#x20; rules:

&#x20;   - if: '$CI\_COMMIT\_BRANCH == "main"'

```



```text

workflow: rules

&#x20;       ↓

Should the pipeline exist?

&#x20;       ↓

Pipeline

&#x20;       ↓

job rules

&#x20;       ↓

Which jobs should exist?

```



> 🧠 \*\*Key takeaway:\*\* `workflow: rules` controls \*\*pipeline\*\* creation; job-level `rules` control \*\*individual job\*\* creation.



> 🚪 \*\*Analogy:\*\* `workflow: rules` is the \*\*building's front door\*\* — if it's locked, nobody gets in. Job `rules` are the \*\*doors to individual rooms\*\* inside.



\---



\## 3. `rules`



`rules` let GitLab \*\*conditionally create\*\* jobs.



```yaml

branch-test:

&#x20; stage: test

&#x20; script:

&#x20;   - echo "This job is running"

&#x20; rules:

&#x20;   - if: '$CI\_COMMIT\_BRANCH == "main"'

```



```text

Is the branch main?

&#x20;      │

&#x20;  YES │ NO

&#x20;   ↓    ↓

&#x20; Run   Don't create

```



> 💡 How GitLab evaluates `rules`:

> - Rules are checked \*\*top to bottom\*\*.

> - The \*\*first matching rule wins\*\* — the rest are ignored.

> - If \*\*no rule matches\*\*, the job is \*\*not added\*\* to the pipeline.

> - `when: never` in a rule explicitly excludes the job.



\---



\## 4. `CI\_COMMIT\_BRANCH`



A predefined variable containing the \*\*branch\*\* associated with the commit.



Examples:



```text

main

feature/login

feature/lesson15-pipeline-control

```



```yaml

rules:

&#x20; - if: '$CI\_COMMIT\_BRANCH == "main"'   # run when the branch is main

```



> ⚠️ Reminder from Lesson 12: `CI\_COMMIT\_BRANCH` is \*\*empty\*\* in Merge Request and tag pipelines.



> 💡 Instead of hard-coding `"main"`, you can use the predefined `$CI\_DEFAULT\_BRANCH`:

> ```yaml

> - if: '$CI\_COMMIT\_BRANCH == $CI\_DEFAULT\_BRANCH'

> ```



\---



\# 🛠️ Hands-On: Rules



\## 5. Hands-On — Branch-Based Rules



```yaml

stages:

&#x20; - test



branch-test:

&#x20; stage: test

&#x20; script:

&#x20;   - echo "This job is running"

&#x20;   - 'echo "Branch: $CI\_COMMIT\_BRANCH"'

&#x20; rules:

&#x20;   - if: '$CI\_COMMIT\_BRANCH == "main"'

```



The exercise was performed on `feature/lesson15-pipeline-control`.



Because `feature/lesson15-pipeline-control` ≠ `main`:



```text

branch-test → Not created ❌

```



This demonstrated that a job-level rule can \*\*prevent a job from being created\*\*.



> 💡 The line `'echo "Branch: $CI\_COMMIT\_BRANCH"'` is wrapped in single quotes because the colon followed by a space (`: `) would otherwise confuse the YAML parser.



\---



\## 6. Running a Job on a Feature Branch



The rule was changed to match the current branch:



```yaml

rules:

&#x20; - if: '$CI\_COMMIT\_BRANCH == "feature/lesson15-pipeline-control"'

```



```text

Rule → TRUE

&#x20;     ↓

Job created

&#x20;     ↓

Job passed ✅

```



This demonstrated \*\*conditional job execution\*\*.



> 💡 To match \*\*all\*\* feature branches instead of one, use a regex with `=\~`:

> ```yaml

> - if: '$CI\_COMMIT\_BRANCH =\~ /^feature\\//'

> ```



\---



\## 7. Multiple Rules



```yaml

rules:

&#x20; - if: '$CI\_COMMIT\_BRANCH == "main"'

&#x20; - if: '$CI\_COMMIT\_BRANCH == "feature/lesson15-pipeline-control"'

```



GitLab evaluates rules \*\*in order\*\*:



```text

Is branch main?

&#x20;    │

&#x20;  YES → Run

&#x20;    │

&#x20;   NO

&#x20;    ↓

Is branch feature/lesson15-pipeline-control?

&#x20;    │

&#x20;  YES → Run

&#x20;    │

&#x20;   NO

&#x20;    ↓

No matching rule → job not created

```



Multiple rules let a job support \*\*multiple conditions\*\* (logical OR).



\---



\# ⏱️ `when` and `allow\_failure`



\## 8. `when`



The `when` keyword controls \*\*how and when\*\* a job runs.



| Value | Meaning |

|---|---|

| `on\_success` | Run if all earlier stages succeeded \*(default)\* |

| `on\_failure` | Run only if an earlier job failed |

| `always` | Run regardless of earlier results |

| `manual` | Wait for a human to click \*\*Play\*\* |

| `never` | Don't run (used inside `rules`) |



\---



\## 9. `when: manual`



`when: manual` makes a job \*\*wait for human action\*\*.



```yaml

deploy:

&#x20; stage: deploy

&#x20; script:

&#x20;   - echo "Deploying application"

&#x20; when: manual

```



The job is created but shows a ▶️ \*\*Play\*\* button.



```text

Build

&#x20; ↓

Test

&#x20; ↓

Deploy (manual)

&#x20; ↓

User clicks Play ▶️

&#x20; ↓

Deployment

```



Commonly used for \*\*deployment\*\* workflows.



\---



\## 10. `when: always`



`when: always` runs a job \*\*regardless\*\* of whether earlier jobs passed or failed.



```yaml

pipeline-report:

&#x20; stage: report

&#x20; script:

&#x20;   - echo "Pipeline completed"

&#x20; when: always

```



Useful for:



\- Reports

\- Cleanup

\- Notifications

\- Log collection



```text

Previous job

&#x20;     ├── Passed ─┐

&#x20;     └── Failed ─┤

&#x20;                 ↓

&#x20;          when: always

&#x20;                 ↓

&#x20;           Run the job

```



> ⚠️ Remember: the `report` stage must be listed under `stages:` — otherwise GitLab reports a "stage does not exist" error (Lesson 12).



\---



\## 11. `when: on\_success`



`on\_success` (the \*\*default\*\*) runs a job only when all jobs in earlier stages have \*\*succeeded\*\*.



```yaml

deploy:

&#x20; stage: deploy

&#x20; script:

&#x20;   - echo "Deploying"

&#x20; when: on\_success

```



```text

Build → ✅ → Test → ✅ → Deploy

```



This is the normal successful pipeline flow.



\---



\## 12. `when: on\_failure`



`on\_failure` runs a job \*\*only when at least one job in an earlier stage failed\*\*.



```yaml

failure-report:

&#x20; stage: report

&#x20; script:

&#x20;   - echo "A previous job failed"

&#x20; when: on\_failure

```



Useful for failure handling, alerts, and diagnostics.



| Earlier stages | `on\_success` job | `on\_failure` job | `always` job |

|---|---|---|---|

| ✅ All passed | Runs | Skipped | Runs |

| ❌ Something failed | Skipped | Runs | Runs |



\---



\## 13. `allow\_failure`



`allow\_failure` controls whether a job's failure \*\*blocks the pipeline\*\*.



```yaml

quality-check:

&#x20; stage: quality

&#x20; script:

&#x20;   - echo "Running quality check"

&#x20;   - exit 1

&#x20; allow\_failure: true

```



```text

quality-check → Failed ⚠️ (allowed)

Pipeline      → Passed with warnings

```



The job shows an \*\*orange warning\*\* icon, later stages still run, and the pipeline does not fail.



> 💡 Useful for \*\*non-blocking\*\* checks — e.g. a new linter the team is still adopting.



\---



\## 14. `allow\_failure` vs `when: manual`



These solve \*\*different problems\*\*:



| Feature | Purpose | Example meaning |

|---|---|---|

| `when: manual` | Wait for human action | "Wait for someone to start this job." |

| `allow\_failure: true` | Failure doesn't block the pipeline | "Run this job, but its failure is acceptable." |



```yaml

deploy:

&#x20; when: manual          # waits for a click

```



```yaml

quality-check:

&#x20; allow\_failure: true   # runs automatically; failure is tolerated

```



\---



\# 🚦 Pipeline-Level Control



\## 15. `workflow: rules`



`workflow: rules` controls whether the \*\*complete pipeline\*\* is created.



```yaml

workflow:

&#x20; rules:

&#x20;   - if: '$CI\_COMMIT\_BRANCH == "main"'

```



```text

Push to main           → Pipeline created ✅

Push to another branch → Pipeline not created ❌

```



\---



\## 16. Job Rule vs Workflow Rule



\*\*Job rule:\*\*



```yaml

test:

&#x20; rules:

&#x20;   - if: '$CI\_COMMIT\_BRANCH == "main"'

```



```text

Pipeline exists

&#x20;      ↓

Test job condition evaluated

&#x20;      ↓

Job may or may not be created

```



\*\*Workflow rule:\*\*



```yaml

workflow:

&#x20; rules:

&#x20;   - if: '$CI\_COMMIT\_BRANCH == "main"'

```



```text

Condition evaluated

&#x20;      ↓

Pipeline may or may not be created

```



> 🧠 `workflow: rules` is evaluated at the \*\*pipeline\*\* level; job `rules` operate at the \*\*job\*\* level.



\---



\## 17. Merge Request Pipelines



A \*\*Merge Request pipeline\*\* runs CI/CD checks when code is proposed for merging.



```text

Feature branch

&#x20;     ↓

Merge Request

&#x20;     ↓

CI/CD Pipeline

&#x20;     ├── Build

&#x20;     ├── Test

&#x20;     ├── Quality

&#x20;     └── Security

&#x20;     ↓

Passed ✅

&#x20;     ↓

Merge

```



Teams can validate changes \*\*before\*\* merging them into the target branch.



\---



\## 18. `CI\_PIPELINE\_SOURCE`



`CI\_PIPELINE\_SOURCE` indicates \*\*what triggered\*\* the pipeline.



```yaml

workflow:

&#x20; rules:

&#x20;   - if: '$CI\_PIPELINE\_SOURCE == "merge\_request\_event"'

```



This means: create the pipeline when it's associated with a \*\*Merge Request\*\* event.



Common values:



| Value | Triggered by |

|---|---|

| `push` | A `git push` |

| `merge\_request\_event` | Creating or updating a Merge Request |

| `web` | \*\*Run pipeline\*\* button in the UI |

| `schedule` | A pipeline schedule |

| `api` | The pipelines API |

| `trigger` | A trigger token |



\---



\## 19. `CI\_COMMIT\_BRANCH` vs `CI\_PIPELINE\_SOURCE`



| Variable | Question it answers | Example values |

|---|---|---|

| `CI\_COMMIT\_BRANCH` | \*\*Which branch\*\* is associated with the commit? | `main`, `feature/login` |

| `CI\_PIPELINE\_SOURCE` | \*\*What caused\*\* the pipeline to run? | `push`, `merge\_request\_event`, `web`, `schedule` |



This distinction is important when designing production pipelines.



\---



\## 20. Combining `rules` and `when`



`when` can be set \*\*inside\*\* a rule:



```yaml

production-deploy:

&#x20; stage: deploy

&#x20; script:

&#x20;   - echo "Production deployment"

&#x20; rules:

&#x20;   - if: '$CI\_COMMIT\_BRANCH == "main"'

&#x20;     when: manual

```



> If the branch is `main`, create the deployment job — but require a \*\*human\*\* to trigger it.



```text

Feature branch

&#x20;     ↓

No production deployment job



main

&#x20;↓

Production deployment job

&#x20;↓

Manual

&#x20;↓

Human clicks Play ▶️

&#x20;↓

Deployment

```



> ⚠️ \*\*Subtle difference:\*\* When `when: manual` is set \*\*inside `rules`\*\*, `allow\_failure` defaults to `false`. The pipeline then shows as \*\*blocked\*\* ⏸️ until someone runs the job. When `when: manual` is set at the \*\*job level\*\* (outside `rules`), `allow\_failure` defaults to `true` and the pipeline can complete without it.



\---



\# 🏭 Production



\## 21. Production-Style Pipeline Control



A production pipeline combining everything:



```yaml

workflow:

&#x20; rules:

&#x20;   - if: '$CI\_PIPELINE\_SOURCE == "merge\_request\_event"'

&#x20;   - if: '$CI\_COMMIT\_BRANCH == "main"'



stages:

&#x20; - test

&#x20; - quality

&#x20; - deploy



test:

&#x20; stage: test

&#x20; script:

&#x20;   - echo "Running tests"

&#x20; rules:

&#x20;   - if: '$CI\_PIPELINE\_SOURCE == "merge\_request\_event"'

&#x20;   - if: '$CI\_COMMIT\_BRANCH == "main"'



quality-check:

&#x20; stage: quality

&#x20; script:

&#x20;   - echo "Running quality checks"

&#x20; allow\_failure: true

&#x20; rules:

&#x20;   - if: '$CI\_PIPELINE\_SOURCE == "merge\_request\_event"'

&#x20;   - if: '$CI\_COMMIT\_BRANCH == "main"'



production-deploy:

&#x20; stage: deploy

&#x20; script:

&#x20;   - echo "Production deployment started"

&#x20;   - echo "Production deployment completed"

&#x20; rules:

&#x20;   - if: '$CI\_COMMIT\_BRANCH == "main"'

&#x20;     when: manual

```



> 💡 This workflow creates pipelines \*\*only\*\* for MRs and `main`. A plain push to a feature branch without an MR creates \*\*no pipeline\*\* — which also prevents \*\*duplicate pipelines\*\* (one for the push and one for the MR).



\---



\## 22. Production Pipeline Flow



\*\*Merge Request:\*\*



```text

Merge Request

&#x20;     ↓

workflow rule matches

&#x20;     ↓

Pipeline created

&#x20;     ↓

Test

&#x20;     ↓

Quality Check

&#x20;     ↓

No production deployment

```



\*\*Main branch:\*\*



```text

main

&#x20;↓

workflow rule matches

&#x20;↓

Pipeline created

&#x20;↓

Test

&#x20;↓

Quality Check

&#x20;↓

Production Deployment

&#x20;↓

Manual approval ▶️

&#x20;↓

Deployment

```



\---



\## 23. Why Manual Production Deployment Is Useful



Production deployments often need \*\*additional controls\*\*:



```text

Code

&#x20;↓

Build

&#x20;↓

Unit Tests

&#x20;↓

SonarQube

&#x20;↓

Security Scan

&#x20;↓

Artifact

&#x20;↓

Manual Approval ▶️

&#x20;↓

Production

```



This reduces the chance of an \*\*unintended\*\* production deployment.



Later in this roadmap, these concepts will be combined with:



\- SonarQube

\- JFrog Artifactory

\- Docker

\- Terraform

\- Kubernetes



\---



\# 📋 Summary



\## 24. Hands-On Project



Branch: `feature/lesson15-pipeline-control`



Exercises demonstrated:



1\. Branch-based rules

2\. Multiple rules

3\. Manual jobs

4\. Allowed failures

5\. Workflow rules

6\. Merge Request pipelines

7\. `CI\_PIPELINE\_SOURCE`

8\. `when: always`

9\. `when: manual`

10\. Combining workflow and job rules



\---



\## 25. Troubleshooting Experience



Pipeline behavior was intentionally changed to see how GitLab evaluates rules:



| What didn't match | Result | Level |

|---|---|---|

| Job-level `rules` | Job → \*\*Not created\*\* | Job control |

| `workflow: rules` | Pipeline → \*\*Not created\*\* | Pipeline control |



> 🔧 \*\*Debugging tip:\*\* If a pipeline doesn't appear at all, check `workflow: rules` first. If the pipeline appears but a job is missing, check that job's `rules`.



\---



\## 26. Important Concepts Summary



| Concept | Meaning |

|---|---|

| `rules` | Controls job creation/execution conditions (first match wins) |

| `workflow: rules` | Controls whether a pipeline is created |

| `CI\_COMMIT\_BRANCH` | Branch of the commit (empty in MR/tag pipelines) |

| `CI\_PIPELINE\_SOURCE` | What triggered the pipeline |

| `when: manual` | Requires manual action |

| `when: always` | Runs regardless of previous results |

| `when: on\_success` | Runs after successful previous work (default) |

| `when: on\_failure` | Runs when previous work fails |

| `allow\_failure` | Allows a job failure without blocking the pipeline |



\---



\## 27. Key Mental Model



```text

&#x20;                GitLab Event

&#x20;                     │

&#x20;                     ↓

&#x20;             workflow: rules

&#x20;          Should pipeline exist?

&#x20;                     │

&#x20;                     ↓

&#x20;                 Pipeline

&#x20;                     │

&#x20;                     ↓

&#x20;              Job-level rules

&#x20;            Should job exist?

&#x20;                     │

&#x20;                     ↓

&#x20;                    Job

&#x20;                     │

&#x20;                     ↓

&#x20;                   when

&#x20;          How should it behave?

&#x20;                     │

&#x20;                     ↓

&#x20;               GitLab Runner

&#x20;                     │

&#x20;                     ↓

&#x20;               Job execution

```



\---



\## 28. Final Lesson 15 Checklist



\- \[x] Understand pipeline control

\- \[x] Understand job-level rules

\- \[x] Understand branch-based rules

\- \[x] Understand multiple rules

\- \[x] Understand `when`

\- \[x] Understand `when: manual`

\- \[x] Understand `when: always`

\- \[x] Understand `when: on\_success`

\- \[x] Understand `when: on\_failure`

\- \[x] Understand `allow\_failure`

\- \[x] Understand `workflow: rules`

\- \[x] Understand pipeline-level control

\- \[x] Understand `CI\_PIPELINE\_SOURCE`

\- \[x] Understand Merge Request pipelines

\- \[x] Combine `rules` with `when`

\- \[x] Design a production-style pipeline flow



\---



\## 29. Final Takeaway



> 🧠 \*\*`workflow: rules` decides whether the pipeline exists, job `rules` decide which jobs exist, and `when` controls how an eligible job behaves.\*\*



```text

Event

&#x20; ↓

workflow: rules → Pipeline?

&#x20; ↓

Job rules       → Job?

&#x20; ↓

when            → How should it run?

&#x20; ↓

Runner

&#x20; ↓

Execution

```



This is the foundation for designing \*\*production-grade\*\* GitLab CI/CD pipelines.



\---



\### ✅ Status: Lesson 15 — Completed



\---



⬅️ \*\*Previous:\*\* Lesson 14 — GitLab Runners | ➡️ \*\*Next:\*\* Lesson 16

