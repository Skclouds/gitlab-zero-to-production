# Lesson 3 - GitLab Fundamentals

## Objective

The objective of this lesson is to understand the fundamental concepts and structure of the GitLab platform.

Topics covered:

* What is GitLab?
* GitLab.com
* GitLab Self-Managed
* GitLab Instance
* GitLab vs GitHub
* Groups
* Subgroups
* Projects
* Namespaces
* Repositories
* Project visibility
* Members and roles
* Issues
* Merge Requests
* CI/CD
* Package Registry
* Container Registry
* GitLab project architecture

---

## 1. What is GitLab?

GitLab is a platform built around Git that helps teams store source code, collaborate, review code, automate testing and builds, manage software delivery, and deploy applications.

Git provides version control.

GitLab provides a broader software development platform around Git.

The GitLab platform can provide capabilities for:

* Git repositories
* Collaboration
* Code review
* Issue tracking
* CI/CD
* Security
* Package management
* Container Registry
* Deployment

---

## 2. Git vs GitLab

### Git

Git is a distributed version-control system.

Its primary purpose is to track changes to source code and maintain version history.

Common Git commands include:

```text
git init
git clone
git add
git commit
git push
git pull
git branch
git merge
```

### GitLab

GitLab is a platform that provides Git repository hosting along with additional software development and DevOps capabilities.

Conceptually:

```text
Git
+
Repository Hosting
+
Collaboration
+
Code Review
+
CI/CD
+
Security
+
Packages
+
Deployment
```

---

## 3. GitLab.com

GitLab.com is the hosted GitLab service.

With GitLab.com:

```text
Developer
    |
    ↓
Internet
    |
    ↓
GitLab.com
    |
    ↓
GitLab Projects
```

The user does not need to install and maintain the GitLab application themselves.

---

## 4. GitLab Self-Managed

GitLab Self-Managed allows an organization to operate its own GitLab environment.

Conceptually:

```text
Company Infrastructure
        |
        ↓
    Linux Server
        |
        ↓
GitLab Self-Managed
        |
   ┌────┼────┐
   ↓    ↓    ↓
Projects Users CI/CD
```

Self-managed environments introduce administrative responsibilities such as:

* Installation
* Configuration
* Upgrades
* Backups
* Monitoring
* Security
* Infrastructure management

GitLab administration will be covered later in the learning roadmap.

---

## 5. GitLab Instance

A GitLab instance is a complete GitLab environment containing resources such as:

* Users
* Groups
* Projects
* CI/CD
* Runners
* Packages
* Security features

Conceptually:

```text
GitLab Instance
│
├── Users
├── Groups
├── Projects
├── CI/CD
├── Runners
├── Packages
└── Security
```

---

## 6. GitLab vs GitHub

GitHub and GitLab both support Git repositories and software development collaboration.

However, they are separate platforms with different features, interfaces, workflows, and integrations.

For this learning project:

```text
GitHub
    ↓
Learning documentation and portfolio

GitLab
    ↓
Actual GitLab hands-on
    ↓
CI/CD learning
    ↓
Production DevOps
```

Git, GitHub, and GitLab should not be treated as the same thing.

---

## 7. Groups

A GitLab Group is used to organize related projects and manage access at a broader level.

Example:

```text
PaviqLabs
│
├── Web Development
├── Mobile Development
├── DevOps
└── Data Engineering
```

Groups become particularly useful when managing multiple projects and teams.

---

## 8. Subgroups

A subgroup is a group inside another group.

Example:

```text
PaviqLabs
│
└── Engineering
    │
    ├── DevOps
    ├── Backend
    └── Frontend
```

Subgroups allow organizations to create a hierarchical project structure.

---

## 9. Projects

A GitLab Project is the main workspace for a software project.

Example:

```text
Group
  |
  ↓
Project
```

A GitLab Project can contain:

```text
Project
│
├── Repository
├── Issues
├── Merge Requests
├── CI/CD Pipelines
├── Project Members
├── Packages
├── Container Registry
└── Settings
```

A GitLab Project is therefore more than just a Git repository.

---

## 10. Repository

A repository stores Git-managed source code and its version history.

Example:

```text
Project
│
└── Repository
    │
    ├── README.md
    ├── source code
    ├── configuration files
    └── Git history
```

Git commands are used to interact with the repository:

```text
git clone
git add
git commit
git push
git pull
```

---

## 11. Project vs Repository

This is an important distinction.

### Repository

Primarily contains:

```text
Source Code
+
Git History
```

### GitLab Project

Provides:

```text
Repository
+
Issues
+
Merge Requests
+
CI/CD
+
Members
+
Settings
+
Packages
+
Security
```

### Interview Answer

A Git repository stores source code and its version history, while a GitLab project provides the repository along with collaboration, issue tracking, merge requests, CI/CD, security, package management, and other project-level capabilities.

---

## 12. Namespace

A namespace identifies where a GitLab project belongs.

Example:

```text
kaushalsingh
    |
    └── gitlab-zero-to-production
```

A project can also belong to a group namespace.

Example:

```text
paviqlabs
    |
    └── engineering
          |
          └── gitlab-zero-to-production
```

Conceptually:

```text
Namespace
    ↓
Project
    ↓
Repository
```

---

## 13. Project Visibility

GitLab projects can have different visibility levels depending on the GitLab environment and configuration.

Common visibility levels include:

```text
Private
Internal
Public
```

### Private

Only authorized users can access the project.

```text
Private Project
      ↓
Authorized Members
```

### Internal

Designed for visibility to authenticated users of the GitLab instance, where supported.

### Public

The project can be visible to users without requiring project membership, subject to the project and instance settings.

---

## 14. Members and Roles

GitLab allows users to be added to projects and groups with different levels of access.

Important GitLab roles include:

```text
Guest
Reporter
Developer
Maintainer
Owner
```

Roles determine what actions a user can perform.

Detailed permissions and access control will be covered in a dedicated lesson later.

---

## 15. Issues

Issues are used to track work, bugs, tasks, and requirements.

Example:

```text
Issue #101
Add login functionality
```

A typical development workflow can be:

```text
Issue
  ↓
Branch
  ↓
Code
  ↓
Commit
  ↓
Merge Request
  ↓
Review
  ↓
Merge
```

---

## 16. Merge Requests

A Merge Request (MR) is used to propose changes from one branch into another.

Example:

```text
feature/login
      |
      | Merge Request
      ↓
    main
```

Merge Requests can provide:

* Code review
* Discussions
* Approvals
* Pipeline results
* Change review
* Branch merging

Merge Requests will be covered in detail in a later lesson.

---

## 17. CI/CD

GitLab provides CI/CD capabilities for automating software development workflows.

A basic workflow is:

```text
Developer
    |
    ↓
Git Push
    |
    ↓
GitLab Pipeline
    |
    ├── Build
    ├── Test
    ├── Security
    └── Deploy
```

GitLab CI/CD configuration is commonly defined using:

```text
.gitlab-ci.yml
```

CI/CD will be studied in detail beginning with the dedicated CI/CD section of this roadmap.

---

## 18. Package Registry

GitLab can provide package-management capabilities through its Package Registry.

It can be used to store and distribute supported software packages.

Conceptually:

```text
Application
    |
    ↓
Build
    |
    ↓
Package
    |
    ↓
GitLab Package Registry
```

---

## 19. Container Registry

GitLab can also provide a Container Registry for storing container images.

Conceptually:

```text
Docker Build
     |
     ↓
Docker Image
     |
     ↓
GitLab Container Registry
```

Later this will be connected with:

* Docker
* CI/CD
* Kubernetes
* JFrog Artifactory

---

## 20. First GitLab Project - Hands-on

A GitLab project was created for this learning journey.

### Project

```text
gitlab-zero-to-production
```

### Namespace

```text
kaushalsingh1715
```

### Default Branch

```text
main
```

### Visibility

```text
Private
```

### Initial Repository

The project was initialized with a README and contains an initial commit.

Project structure:

```text
gitlab-zero-to-production
│
└── README.md
```

---

## 21. GitHub vs GitLab Learning Architecture

For this learning journey, GitHub and GitLab have different purposes.

```text
                 Learning Journey
                       |
             ┌─────────┴─────────┐
             ↓                   ↓
          GitHub               GitLab
             |                   |
      Documentation          Hands-on Lab
             |                   |
      Lesson Notes            Projects
             |                   |
       Portfolio              CI/CD
                                 |
                              DevOps
```

This allows the learning documentation to remain available as a portfolio while GitLab is used for practical exercises.

---

## 22. GitLab Project Architecture

The basic hierarchy learned in this lesson is:

```text
GitLab Instance
       |
       ├── Users
       |
       └── Groups
            |
            └── Subgroups
                 |
                 └── Projects
                      |
                      ├── Repository
                      ├── Issues
                      ├── Merge Requests
                      ├── CI/CD
                      ├── Packages
                      ├── Container Registry
                      └── Settings
```

This hierarchy is one of the most important mental models for understanding GitLab.

---

## 23. Key Takeaways

The important concepts from Lesson 3 are:

```text
Git
 ↓
Version Control

GitLab
 ↓
DevOps Platform around Git

Instance
 ↓
Complete GitLab Environment

Group
 ↓
Organization of Projects

Subgroup
 ↓
Nested Group

Project
 ↓
Software Development Workspace

Repository
 ↓
Source Code + Git History

Issue
 ↓
Work Tracking

Merge Request
 ↓
Code Review + Merge

CI/CD
 ↓
Automation

Registry
 ↓
Package / Container Storage
```

---

## 24. Lesson Status

### Concepts

* [x] Understand GitLab
* [x] Understand GitLab.com
* [x] Understand GitLab Self-Managed
* [x] Understand GitLab Instance
* [x] Understand GitLab vs GitHub
* [x] Understand Groups
* [x] Understand Subgroups
* [x] Understand Projects
* [x] Understand Repositories
* [x] Understand Namespaces
* [x] Understand Project Visibility
* [x] Understand Members and Roles
* [x] Understand Issues
* [x] Understand Merge Requests
* [x] Understand CI/CD
* [x] Understand Package Registry
* [x] Understand Container Registry

### Hands-on

* [x] Create GitLab account/project
* [x] Create `gitlab-zero-to-production` project
* [x] Use `main` as the default branch
* [x] Create an initial README
* [x] Verify the GitLab project

---

## Key Lesson 3 Principle

The most important concept from this lesson is:

```text
Git ≠ GitHub ≠ GitLab
```

Git is the version-control system.

GitHub and GitLab are platforms built around Git.

GitLab additionally provides an extensive set of capabilities for collaboration, CI/CD, security, package management, and software delivery.

This distinction is important for both practical DevOps work and technical interviews.
