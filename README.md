# 🚀 GitLab Zero to Production

A complete **hands-on learning journey** to master GitLab — from Git fundamentals to production-grade DevOps and CI/CD.

Every lesson is documented with concepts, real-world explanations, hands-on practice, troubleshooting notes, and key takeaways.

---

## 🎯 Learning Objective

Learn GitLab from zero to production through:

| Area | Topics |
|---|---|
| **Foundations** | Git fundamentals, GitLab repositories, branching |
| **Collaboration** | Merge Requests, Issues, users, groups & permissions |
| **Security** | Protected branches, authentication, CI/CD secrets, security scanning |
| **CI/CD** | GitLab CI/CD, `.gitlab-ci.yml`, GitLab Runner, pipeline control |
| **Language Pipelines** | Java, Python, Node.js, Angular |
| **DevOps Toolchain** | Docker, Container Registry, SonarQube, JFrog Artifactory |
| **Infrastructure** | Terraform, Kubernetes |
| **Capstone** | Production CI/CD architecture |

---

## 🧭 Learning Approach

Every topic is learned through:

1. 📖 **Concept** — what it is and why it exists
2. 🌍 **Real-world explanation** — analogies and production examples
3. 🛠️ **Hands-on practice** — performed on a real GitLab project
4. 🎯 **Challenge** — apply the concept independently
5. 🔧 **Troubleshooting** — real errors encountered and how they were fixed
6. 📝 **GitHub documentation** — a README for every lesson
7. 💼 **Interview preparation** — key takeaways and concepts to explain

---

## 📈 Progress

### ✅ Completed

| # | Lesson | Folder |
|---|---|---|
| 1 | Git & GitLab Fundamentals | [`01-git-gitlab-fundamentals`](01-git-gitlab-fundamentals/) |
| 2 | *(title to add)* | — |
| 3 | *(title to add)* | — |
| 4 | *(title to add)* | — |
| 5 | Git Branching | [`05-git-branching`](05-git-branching/) |
| 6 | GitLab Merge Requests | [`06-merge-requests`](06-merge-requests/) |
| 7 | GitLab Issues & Project Management | [`07-issues-project-management`](07-issues-project-management/) |
| 8 | GitLab Users, Groups & Permissions | [`08-users-groups-permissions`](08-users-groups-permissions/) |
| 9 | Protected Branches & Repository Security | [`09-protected-branches`](09-protected-branches/) |
| 10 | GitLab Authentication (SSH & Tokens) | [`10-authentication`](10-authentication/) |
| 11 | GitLab CI/CD Fundamentals | [`11-cicd-fundamentals`](11-cicd-fundamentals/) |
| 12 | `.gitlab-ci.yml` Deep Dive | [`12-gitlab-ci-yml-deep-dive`](12-gitlab-ci-yml-deep-dive/) |
| 13 | GitLab CI/CD Variables & Secrets | [`13-cicd-variables-secrets`](13-cicd-variables-secrets/) |
| 14 | GitLab Runners (Self-Managed Windows Runner) | [`14-gitlab-runners`](14-gitlab-runners/) |
| 15 | Advanced GitLab Pipeline Control | [`15-pipeline-control`](15-pipeline-control/) |
| 16 | Java CI/CD with GitLab (Maven + JUnit) | [`16-java-cicd`](16-java-cicd/) |
| 17 | Python CI/CD with GitLab (pytest) | [`17-python-cicd`](17-python-cicd/) |

### 🔜 Upcoming

- [ ] Node.js CI/CD Pipeline
- [ ] Angular CI/CD Pipeline
- [ ] Docker & GitLab Container Registry
- [ ] SonarQube Integration
- [ ] GitLab Security Scanning (SAST, Dependency & Secret Detection)
- [ ] JFrog Artifactory Integration
- [ ] Terraform Integration
- [ ] Kubernetes Deployment
- [ ] Production Capstone Project

---

## 🗂️ Repository Structure

Each lesson lives in its own numbered folder with a `README.md`:

```text
gitlab-zero-to-production/
├── README.md
├── 01-git-gitlab-fundamentals/
├── 02-.../
├── 03-.../
├── 04-.../
├── 05-git-branching/
├── 06-merge-requests/
├── 07-issues-project-management/
├── 08-users-groups-permissions/
├── 09-protected-branches/
├── 10-authentication/
├── 11-cicd-fundamentals/
├── 12-gitlab-ci-yml-deep-dive/
├── 13-cicd-variables-secrets/
├── 14-gitlab-runners/
├── 15-pipeline-control/
├── 16-java-cicd/
├── 17-python-cicd/
└── ...upcoming lessons
```

---

## 🧪 Hands-On Environment

| Component | Details |
|---|---|
| GitLab project | `gitlab.com/kaushalsingh1715/gitlab-zero-to-production` |
| Documentation repo | `github.com/Skclouds/gitlab-zero-to-production` (this repository) |
| Git authentication | SSH (ED25519 key) |
| CI/CD Runner | Self-managed Windows Runner (`kaushal-windows-runner`, tag `windows`) |
| Executor | Shell (Windows PowerShell) |
| Languages | Java 21 + Maven, Python 3.14 + pytest |

---

## 🏗️ Target Architecture

The end goal of this journey:

```text
Developer
    ↓
Feature Branch → Merge Request (review + approvals)
    ↓
GitLab CI/CD Pipeline
    ├── Build
    ├── Unit Tests
    ├── SonarQube Quality Gate
    └── Security Scanning
    ↓
Protected main
    ↓
Package Artifact → JFrog Artifactory
    ↓
Docker Image → Container Registry
    ↓
Terraform (infrastructure)
    ↓
Kubernetes (deployment) ✅
```
