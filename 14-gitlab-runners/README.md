# Lesson 14 — GitLab Runners

## 🎯 Overview

GitLab CI/CD pipelines define **what work needs to be performed**, while GitLab Runners are responsible for **actually executing that work**.

A **Runner** is an application that connects to GitLab and executes the jobs defined in `.gitlab-ci.yml`.

This lesson covers Runner architecture, Runner types and scopes, executors, tags, self-managed Runner setup on Windows, troubleshooting, and hands-on execution.

---

## 📚 Table of Contents

**Concepts**

1. [What Is a GitLab Runner?](#1-what-is-a-gitlab-runner)
2. [GitLab vs GitLab Runner](#2-gitlab-vs-gitlab-runner)
3. [Why Do We Need Runners?](#3-why-do-we-need-runners)
4. [GitLab Runner Architecture](#4-gitlab-runner-architecture)
5. [GitLab-Hosted vs Self-Managed Runners](#5-gitlab-hosted-vs-self-managed-runners)
6. [Runner Scopes](#6-runner-scopes)
7. [Runner Executors](#7-runner-executors)
8. [Shell Executor](#8-shell-executor)
9. [Docker Executor](#9-docker-executor)
10. [Kubernetes Executor](#10-kubernetes-executor)
11. [Runner Tags](#11-runner-tags)
12. [Why Runner Tags Matter](#12-why-runner-tags-matter)
13. [Runner Lifecycle](#13-runner-lifecycle)

**Hands-On**

14. [Hands-On Environment](#14-hands-on-environment)
15. [Creating the Project Runner](#15-creating-the-project-runner)
16. [Installing GitLab Runner on Windows](#16-installing-gitlab-runner-on-windows)
17. [Registering the Runner](#17-registering-the-runner)
18. [Running GitLab Runner as a Windows Service](#18-running-gitlab-runner-as-a-windows-service)
19. [Verifying Runner Status](#19-verifying-runner-status)
20. [First Job Using the Self-Managed Runner](#20-first-job-using-the-self-managed-runner)
21. [Runner Troubleshooting — `pwsh` Not Found](#21-runner-troubleshooting--pwsh-not-found)
22. [Fixing the Shell Executor](#22-fixing-the-shell-executor)
23. [Successful Runner Execution](#23-successful-runner-execution)
24. [Important Lesson From the Failure](#24-important-lesson-from-the-failure)

**Production & Summary**

25. [Shell Executor vs Docker Executor](#25-shell-executor-vs-docker-executor)
26. [Production Considerations](#26-production-considerations)
27. [Key Commands](#27-key-commands)
28. [Important Concepts](#28-important-concepts)
29. [Final Architecture](#29-final-architecture)
30. [Lesson 14 Outcome](#30-lesson-14-outcome)
31. [Key Takeaway](#31-key-takeaway)

---

## 1. What Is a GitLab Runner?

A **GitLab Runner** is an application that **executes GitLab CI/CD jobs**.

Consider this pipeline:

```yaml
test:
  stage: test
  script:
    - echo "Running tests"
```

GitLab creates the job, but the command `echo "Running tests"` must actually be executed **somewhere**. The Runner provides that execution environment.

### 👔 Simple analogy

GitLab is the **manager**; the Runner is the **worker**.

```text
GitLab
  │
  │  "Run this job"
  ↓
GitLab Runner
  │
  │  Executes commands
  ↓
Machine / Container / Pod
```

> 🧠 **GitLab decides what needs to be done; the Runner actually does the work.**

---

## 2. GitLab vs GitLab Runner

| GitLab | GitLab Runner |
|---|---|
| Stores repositories | Executes CI/CD jobs |
| Manages branches | Runs scripts |
| Manages Merge Requests | Builds applications |
| Manages Issues | Runs tests |
| Creates pipelines | Executes pipeline jobs |
| Stores CI/CD variables | Receives required variables during jobs |
| Controls permissions | Provides the execution environment |
| Displays job results | Performs the actual job |

Pipeline flow:

```text
Developer
    │  git push
    ↓
GitLab
    │  Creates pipeline
    ↓
Pipeline
    │  Creates job
    ↓
GitLab Runner
    │  Executes script
    ↓
Job Result
    ↓
Passed / Failed
```

---

## 3. Why Do We Need Runners?

A CI/CD job needs an environment where its commands can run — **with the right tools installed**.

| Project | Job script | The Runner needs |
|---|---|---|
| Java | `java --version`<br>`mvn test` | Java and Maven |
| Python | `python --version`<br>`pytest` | Python and dependencies |
| Docker | `docker build -t my-app .` | Access to Docker |

A Runner provides the **infrastructure** required to execute CI/CD jobs.

---

## 4. GitLab Runner Architecture

```text
                    GitLab
                      │
                 CI/CD Pipeline
                      │
                      ↓
                 Pipeline Job
                      │
                      ↓
                GitLab Runner
                      │
              ┌───────┼───────┐
              │       │       │
           Shell    Docker  Kubernetes
              │       │       │
              ↓       ↓       ↓
            Host  Container   Pod
```

The exact execution environment depends on the Runner's **executor**.

---

## 5. GitLab-Hosted vs Self-Managed Runners

### 5.1 GitLab-hosted Runner

GitLab provides and maintains the Runner infrastructure.

```text
GitLab → GitLab-hosted Runner → CI/CD Job
```

### 5.2 Self-managed Runner

Installed and maintained **by you or your organization**, on infrastructure such as:

- Windows machine
- Linux server
- Virtual machine / cloud VM
- Container infrastructure
- Kubernetes environment

```text
GitLab → Self-managed Runner → Organization's infrastructure
```

### Comparison

| | GitLab-hosted | Self-managed |
|---|---|---|
| Who maintains it | GitLab | You / your organization |
| Setup effort | None — works immediately | Install, register, maintain |
| Custom software / OS | Limited | ✅ Anything you install |
| Access to internal networks | ❌ | ✅ |
| Specialized hardware (e.g. GPU) | Limited | ✅ |
| Custom security requirements | Limited | ✅ |
| Cost model | Uses compute minutes quota | Your own infrastructure |

> 💡 Lesson 11's first pipeline ran on a **GitLab-hosted** runner. This lesson adds a **self-managed** one.

---

## 6. Runner Scopes

Runners can be attached at different levels:

| Scope | Available to | Typical use |
|---|---|---|
| **Instance Runner** | All projects on the GitLab instance (as configured by admins) | Shared general-purpose CI |
| **Group Runner** | All projects within a group and its subgroups | Team-wide infrastructure |
| **Project Runner** | One specific project | Specialized environment |

```text
GitLab Instance ── Instance Runner
     │
     └── Group ── Group Runner
            │
            ├── Project A
            ├── Project B
            └── Project C ── Project Runner
```

> 💡 This mirrors the group/subgroup/project hierarchy from Lesson 8.

---

## 7. Runner Executors

An **executor** determines **how** the Runner executes a job.

Common executors:

- **Shell** ← used in this lesson
- **Docker**
- **Kubernetes**
- SSH
- VirtualBox
- Custom
- Other supported executors

---

## 8. Shell Executor

With the **Shell** executor, the Runner executes commands **directly on the host operating system**.

Our setup:

```text
GitLab
   ↓
GitLab Runner
   ↓
Shell Executor
   ↓
Windows
   ↓
PowerShell
   ↓
CI/CD commands
```

Example:

```yaml
test:
  script:
    - python --version
    - echo "Running tests"
```

> ⚠️ Because commands run directly on the host, **all required software must already be installed** on the Runner machine and available on its `PATH`.

---

## 9. Docker Executor

The **Docker** executor runs each job inside a **fresh Docker container**.

```text
GitLab
   ↓
GitLab Runner
   ↓
Docker
   ↓
Container
   ↓
CI/CD Job
```

Example:

```yaml
test:
  image: python:3.12
  script:
    - python --version
```

The `image` provides the required environment — so jobs are **consistent and reproducible**, regardless of what's installed on the host.

---

## 10. Kubernetes Executor

The **Kubernetes** executor runs each job in a **Kubernetes Pod**.

```text
GitLab
   ↓
GitLab Runner
   ↓
Kubernetes
   ↓
Pod
   ↓
CI/CD Job
```

Especially useful for **scalable** CI/CD infrastructure.

> 💡 Kubernetes-based GitLab CI/CD is covered in later lessons.

---

## 11. Runner Tags

**Tags** determine which Runners are **eligible** to execute a job.

Our Runner was configured with the tag `windows`, so the job requested it:

```yaml
windows-runner-test:
  stage: test
  tags:
    - windows
  script:
    - echo "Hello from my self-managed GitLab Runner!"
```

```text
Job
 │  tags: windows
 ↓
Runner
 │  tag: windows
 ↓
Eligible Runner ✅
```

> 💡 Tag matching rules:
> - A job runs only on a Runner that has **all** of the job's tags.
> - A job **without** tags runs only on Runners configured to **"Run untagged jobs"**. Otherwise, it goes to GitLab-hosted runners (on GitLab.com) or stays **pending**.

---

## 12. Why Runner Tags Matter

An organization may have multiple Runners:

| Runner | Tag |
|---|---|
| Runner 1 | `docker` |
| Runner 2 | `java` |
| Runner 3 | `windows` |
| Runner 4 | `kubernetes` |

A job picks its infrastructure by tag:

```yaml
tags:
  - windows   # → Runner 3
```

```yaml
tags:
  - docker    # → Runner 1
```

This controls **which infrastructure is used for which jobs**.

---

## 13. Runner Lifecycle

```text
Runner starts
      ↓
Runner connects to GitLab
      ↓
Runner waits (polls) for jobs
      ↓
GitLab assigns a compatible job
      ↓
Runner prepares environment
      ↓
Runner downloads source code
      ↓
Runner executes script
      ↓
Runner collects job result
      ↓
Result sent to GitLab
```

> 💡 The Runner **pulls** jobs from GitLab — GitLab never connects *into* your machine. That's why a Runner behind a firewall or on your laptop still works, as long as it can reach GitLab outbound.

---

# 🛠️ Hands-On

## 14. Hands-On Environment

A self-managed Runner was created for the project `gitlab-zero-to-production`:

| Setting | Value |
|---|---|
| Runner name | `kaushal-windows-runner` |
| Operating system | Windows |
| Architecture | `windows/amd64` |
| Executor | `shell` |
| Tag | `windows` |
| Runner version | `19.4.1` |

---

## 15. Creating the Project Runner

The Runner was created in GitLab from:

```text
Project
  ↓
Settings
  ↓
CI/CD
  ↓
Runners
  ↓
Create project runner
```

| Field | Value |
|---|---|
| Description | `kaushal-windows-runner` |
| Tag | `windows` |

GitLab then displayed a **runner authentication token** (starts with `glrt-`) used in the registration step.

---

## 16. Installing GitLab Runner on Windows

Create a dedicated directory:

```cmd
mkdir C:\GitLab-Runner
```

The executable was downloaded into it:

```text
C:\GitLab-Runner\gitlab-runner-windows-amd64.exe
```

Verify the installation:

```cmd
gitlab-runner-windows-amd64.exe --version
```

```text
Version:      19.4.1
Git revision: 3c39fceb
Git branch:   19-4-stable
GO version:   go1.26.5
OS/Arch:      windows/amd64
```

> 💡 Many people rename the file to `gitlab-runner.exe` to shorten the commands.

---

## 17. Registering the Runner

The Runner was registered against GitLab.com:

```cmd
gitlab-runner-windows-amd64.exe register --url https://gitlab.com --token <runner-authentication-token>
```

During registration, the following was configured:

| Prompt | Value |
|---|---|
| Runner name | `kaushal-windows-runner` |
| Executor | `shell` |

The configuration was saved to:

```text
C:\GitLab-Runner\config.toml
```

> 🔐 `config.toml` contains the Runner's **authentication token**. Never commit it to Git or share it publicly. If it leaks, delete the Runner in GitLab and create a new one.

---

## 18. Running GitLab Runner as a Windows Service

The first attempt from a normal Command Prompt failed:

```text
FATAL: Failed to install gitlab-runner: Access is denied.
```

**Cause:** Installing a Windows service requires **administrator** permissions.

**Fix:** Reopen Command Prompt with **Run as administrator**, then:

```cmd
cd C:\GitLab-Runner
gitlab-runner-windows-amd64.exe install
gitlab-runner-windows-amd64.exe start
gitlab-runner-windows-amd64.exe status
```

```text
gitlab-runner: Service is running
```

> 💡 Running as a **service** means the Runner starts automatically with Windows and keeps working after you close the terminal.

---

## 19. Verifying Runner Status

In GitLab (**Settings → CI/CD → Runners**), the Runner appeared as:

| Runner | Status |
|---|---|
| `kaushal-windows-runner` | 🟢 **Online** |

This confirmed that GitLab could communicate with the self-managed Runner.

---

## 20. First Job Using the Self-Managed Runner

A dedicated branch was created:

```bash
git switch -c feature/lesson14-self-managed-runner
```

The CI configuration was updated:

```yaml
windows-runner-test:
  stage: test
  tags:
    - windows
  script:
    - echo "Hello from my self-managed GitLab Runner!"
    - echo "This job is running on my Windows machine"
    - echo "Runner test completed successfully"
    - whoami
    - hostname
```

The branch was pushed, and the pipeline created the job `windows-runner-test`.

---

## 21. Runner Troubleshooting — `pwsh` Not Found

The **first execution failed**. The job log showed:

```text
Using Shell (pwsh) executor...
```

followed by:

```text
ERROR: Job failed (system failure):
prepare environment: failed to start process:
starting OS command: exec: "pwsh":
executable file not found in %PATH%
```

### 🔍 Problem

| | |
|---|---|
| `pwsh` | **PowerShell 7+** (newer, cross-platform) — the default for new Windows Runner registrations |
| `powershell` | **Windows PowerShell 5.1** — built into Windows |

The Runner was configured to use `pwsh`, but PowerShell 7 was **not installed** on the machine. The Runner couldn't start the job's shell.

---

## 22. Fixing the Shell Executor

Open the Runner configuration:

```cmd
notepad config.toml
```

Change the shell to built-in Windows PowerShell:

```toml
[[runners]]
  name = "kaushal-windows-runner"
  executor = "shell"
  shell = "powershell"    # was: "pwsh"
```

Restart the service:

```cmd
gitlab-runner-windows-amd64.exe stop
gitlab-runner-windows-amd64.exe start
gitlab-runner-windows-amd64.exe status
```

```text
gitlab-runner: Service is running
```

The failed job was then **retried** from GitLab.

> 💡 **Alternative fix:** Install PowerShell 7 (`pwsh`) and keep the default setting. Either works — the key is that the configured shell must exist on the host.

---

## 23. Successful Runner Execution

After correcting the shell configuration, the job **passed**. ✅

```text
GitLab
   ↓
Pipeline
   ↓
windows-runner-test
   │  tags: windows
   ↓
kaushal-windows-runner
   ↓
Shell Executor (powershell)
   ↓
Windows Machine
   ↓
Job Passed ✅
```

> 💡 In the job log, `whoami` likely printed `nt authority\system` — because the Runner service runs as the Windows **Local System** account by default, not as your user.

---

## 24. Important Lesson From the Failure

> 🧠 **A Runner being Online does not guarantee that every job will execute successfully.**

There are multiple layers, and a failure can occur at any of them:

```text
GitLab
   ↓
Runner Registration
   ↓
Runner Online
   ↓
Runner Tags
   ↓
Executor
   ↓
Host Environment
   ↓
Required Tools
   ↓
CI/CD Script
```

In this hands-on:

| Layer | Result |
|---|---|
| Runner registration | ✅ SUCCESS |
| Runner online | ✅ SUCCESS |
| Tag matching | ✅ SUCCESS |
| Runner selection | ✅ SUCCESS |
| Executor startup | ❌ **FAILED** — `pwsh` not found |

After correcting the shell configuration:

| Layer | Result |
|---|---|
| Executor startup | ✅ SUCCESS |
| CI/CD job | ✅ SUCCESS |

> 🔧 **Troubleshooting tip:** The words `system failure` in a job error point to the **Runner/environment**, not to your script. A failing *command* in your script shows a normal `Job failed: exit code 1` instead.

---

# 🏭 Production & Summary

## 25. Shell Executor vs Docker Executor

| Feature | Shell | Docker |
|---|---|---|
| Execution | Directly on host | Inside a container |
| Environment isolation | Low | Higher |
| Setup | Simple | Requires Docker |
| Host dependencies | Important | Mostly defined by the image |
| Startup overhead | Low | Container startup required |
| Environment consistency | Depends on host | More consistent |
| Useful for | Simple / internal jobs | Reproducible CI environments |

---

## 26. Production Considerations

Self-managed Runners should be treated as **infrastructure**.

### 1. Runner security 🔐

Never expose Runner authentication tokens. Never commit `config.toml` to a repository.

### 2. Least privilege

The Runner should have only the permissions required for its jobs.

> ⚠️ A Shell Runner installed as a Windows service runs as **Local System** — a very powerful account. Any job script can do anything that account can. For production, run the service under a **dedicated, limited user account**.

### 3. Runner isolation

Don't let untrusted projects or users run arbitrary commands on sensitive Runner machines.

### 4. Software management

The Runner host must have the tools its jobs need:

```text
Java · Maven · Python · Node.js · Docker · Terraform · kubectl
```

### 5. Meaningful tags

```text
windows · linux · docker · java · python · production · development
```

### 6. Monitoring

Monitor Runner availability and job failures. An **Offline** Runner leaves tagged jobs stuck in **pending**.

---

## 27. Key Commands

Run from `C:\GitLab-Runner` (service commands need an **administrator** prompt):

| Command | Purpose |
|---|---|
| `gitlab-runner-windows-amd64.exe --version` | Check Runner version |
| `gitlab-runner-windows-amd64.exe register --url https://gitlab.com --token <token>` | Register the Runner |
| `gitlab-runner-windows-amd64.exe install` | Install as a Windows service |
| `gitlab-runner-windows-amd64.exe start` | Start the Runner service |
| `gitlab-runner-windows-amd64.exe stop` | Stop the Runner service |
| `gitlab-runner-windows-amd64.exe restart` | Restart the Runner service |
| `gitlab-runner-windows-amd64.exe status` | Check service status |
| `gitlab-runner-windows-amd64.exe verify` | Check the Runner can authenticate with GitLab |
| `notepad config.toml` | Open Runner configuration |

---

## 28. Important Concepts

| Concept | Meaning |
|---|---|
| **GitLab** | Orchestrates the CI/CD workflow |
| **Runner** | Executes CI/CD jobs |
| **Executor** | Determines *how* the Runner executes jobs |
| **Tag** | Determines *which* Runners are eligible for a job |
| **Self-managed Runner** | Runner infrastructure maintained by the user or organization |
| **Shell executor** | Executes commands directly on the Runner host |
| **Runner Online** | The Runner is connected and available to GitLab |

---

## 29. Final Architecture

```text
                    Developer
                        │
                        │ git push
                        ↓
                 GitLab Repository
                        │
                        ↓
                   CI/CD Pipeline
                        │
                        ↓
                      CI Job
                        │
                  tags: windows
                        │
                        ↓
             kaushal-windows-runner
                        │
                        ↓
                  Shell Executor
                        │
                        ↓
                 Windows Machine
                        │
                        ↓
                 Execute Commands
                        │
                        ↓
                   Job Result
                        │
                        ↓
                 GitLab: Passed ✅
```

---

## 30. Lesson 14 Outcome

- [x] Understand GitLab Runner
- [x] Understand GitLab vs Runner
- [x] Understand GitLab-hosted Runners
- [x] Understand self-managed Runners
- [x] Understand Runner scopes
- [x] Understand Runner executors
- [x] Understand Shell executor
- [x] Understand Docker executor
- [x] Understand Kubernetes executor
- [x] Understand Runner tags
- [x] Create a project Runner
- [x] Install GitLab Runner on Windows
- [x] Register a self-managed Runner
- [x] Configure a Shell executor
- [x] Install Runner as a Windows service
- [x] Verify Runner status
- [x] Run a CI/CD job using a Runner tag
- [x] Troubleshoot a Runner environment failure
- [x] Successfully execute a CI/CD job on a self-managed Windows Runner

---

## 31. Key Takeaway

> 🧠 **GitLab orchestrates CI/CD; GitLab Runner executes CI/CD jobs.**

A production CI/CD architecture depends not only on a valid `.gitlab-ci.yml`, but also on **correctly configured Runner infrastructure**.

```text
.gitlab-ci.yml
      ↓
   Pipeline
      ↓
     Job
      ↓
Runner Selection
      ↓
     Tags
      ↓
   Executor
      ↓
Execution Environment
      ↓
  Job Result
```

---

### ✅ Status: Lesson 14 — Completed

