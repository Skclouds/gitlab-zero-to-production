# Lesson 7 — GitLab Issues & Project Management

## 🎯 Objective

The objective of this lesson was to understand **GitLab Issues** and basic **project-management workflows**.

The lesson covered:

- GitLab Issues
- Issue titles and descriptions
- Labels
- Assignees
- Milestones
- Issue lifecycle
- Issue comments
- Issue and Merge Request relationships
- Connecting an Issue to development work
- Linking an Issue with a Merge Request
- Automatic Issue closing through Merge Request keywords

A **complete hands-on workflow** was performed using the actual GitLab project.

---

## 📚 Table of Contents

**Concepts**

1. [What Is a GitLab Issue?](#1-what-is-a-gitlab-issue)
2. [Issue vs Merge Request](#2-issue-vs-merge-request)
3. [Why GitLab Issues Are Useful](#3-why-gitlab-issues-are-useful)
4. [Issue Title](#4-issue-title)
5. [Issue Description](#5-issue-description)
6. [Labels](#6-labels)
7. [Assignees](#7-assignees)
8. [Milestones](#8-milestones)
9. [Issue Lifecycle](#9-issue-lifecycle)
10. [Issue Comments](#10-issue-comments)
11. [Issue and Merge Request Relationship](#11-issue-and-merge-request-relationship)

**Hands-On**

12. [Hands-On Environment](#12-hands-on-environment)
13. [Practical 1 — Create GitLab Issue](#13-practical-1--create-gitlab-issue)
14. [Practical 2 — Create Feature Branch](#14-practical-2--create-feature-branch)
15. [Practical 3 — Create Practice Change](#15-practical-3--create-practice-change)
16. [Practical 4 — Commit the Change](#16-practical-4--commit-the-change)
17. [Practical 5 — Push Feature Branch](#17-practical-5--push-feature-branch)
18. [Practical 6 — Create Merge Request](#18-practical-6--create-merge-request)
19. [Closing Keywords: `Closes #1`](#19-closing-keywords-closes-1)
20. [Complete Lesson 7 Workflow](#20-complete-lesson-7-workflow)

**Summary**

21. [Important Git Commands](#21-important-git-commands)
22. [Production DevOps Workflow](#22-production-devops-workflow)
23. [Issue Management in a DevOps Environment](#23-issue-management-in-a-devops-environment)
24. [Key Learnings](#24-key-learnings)
25. [Lesson 7 Outcome](#25-lesson-7-outcome)

---

## 1. What Is a GitLab Issue?

A **GitLab Issue** is used to track a unit of work — a problem, feature request, bug, improvement, or documentation task.

An Issue provides a **central location** where a team can describe the work, assign responsibility, discuss the task, and track its progress.

Examples:

```text
Issue: Fix login functionality
Issue: Add Docker support
```

---

## 2. Issue vs Merge Request

This is an important distinction.

| | Question it answers | Example |
|---|---|---|
| **Issue** | *What work needs to be done?* | `#1 Create GitLab Issues and Project Management Documentation` |
| **Merge Request** | *What code changes are being proposed?* | `Add Lesson 7 issue practice` |

The relationship:

```text
Issue
"What needs to be done?"
        │
        v
Development
        │
        v
Merge Request
"What code changes implement it?"
        │
        v
Main Branch
```

> 💡 **Analogy:** An Issue is the **ticket** at a repair shop describing the problem. The Merge Request is the **actual repair work** submitted for inspection.

---

## 3. Why GitLab Issues Are Useful

Issues provide a structured way to manage work. They can be used for:

- Bug tracking
- Feature requests
- Development tasks
- Documentation work
- Improvements
- Technical tasks
- Investigation work

Instead of tracking work through separate spreadsheets, messages, or emails, teams keep the work item **inside the GitLab project**, right next to the code.

---

## 4. Issue Title

An Issue title should clearly communicate the purpose of the work.

Good examples:

```text
Fix Jenkins pipeline failure
Add Docker image build stage
```

For this lesson:

```text
Create GitLab Issues and Project Management Documentation
```

A clear title allows team members to understand the task quickly.

---

## 5. Issue Description

The description provides the **detailed requirements** for the task.

For this lesson, the Issue description was:

```markdown
## Objective

Document the GitLab Issues and Project Management workflow.

## Tasks

- Understand GitLab Issues
- Understand labels
- Understand assignees
- Understand milestones
- Understand Issue and Merge Request relationships
- Document the practical workflow

## Acceptance Criteria

- Issue is created successfully.
- Issue contains a clear description.
- Appropriate label is assigned.
- Issue is assigned to the responsible user.
- Issue is linked to the Lesson 7 work.
```

> 💡 **Acceptance Criteria** define what "done" means. They make the expected outcome clear to everyone.

---

## 6. Labels

Labels **categorize** Issues. Common examples:

| Type | Area | Priority |
|---|---|---|
| `bug` | `frontend` | `urgent` |
| `feature` | `backend` | |
| `enhancement` | `devops` | |
| `documentation` | | |

For this lesson, the following label was created and applied:

```text
documentation
```

Labels become increasingly useful as the number of Issues grows — you can filter and search by them.

---

## 7. Assignees

An **assignee** identifies the person responsible for the Issue.

For this lesson:

```text
Assignee: kaushal singh
```

Assigning ownership makes responsibility clear.

---

## 8. Milestones

A **milestone** groups multiple Issues around a larger objective, release, or project phase.

```text
Milestone: GitLab CI/CD v1

Issues:
├── Create GitLab Runner
├── Create .gitlab-ci.yml
├── Configure variables
├── Add automated tests
└── Integrate SonarQube
```

A milestone provides a **higher-level view** of related work and its overall progress.

---

## 9. Issue Lifecycle

A simple Issue workflow:

```text
Open
  │
  v
Work in Progress
  │
  v
Completed
  │
  v
Closed
```

The exact workflow varies by team. Many teams represent these stages with labels (e.g. `workflow::in-progress`) and visualize them on an **Issue Board**.

---

## 10. Issue Comments

Issues can be used for team communication:

```text
Developer: I identified the root cause.
Reviewer:  Please add a test case.
Developer: Test case added.
```

This keeps the discussion **attached to the work item** instead of scattered across chat and email.

---

## 11. Issue and Merge Request Relationship

One of the most useful GitLab workflows is connecting an Issue to the code that implements it:

```text
Issue
   │
   v
Feature Branch
   │
   v
Commit
   │
   v
Merge Request
   │
   v
Main Branch
```

This creates **traceability** between the original requirement and the resulting code change.

---

# 🛠️ Hands-On

## 12. Hands-On Environment

| Item | Value |
|---|---|
| GitLab project | `gitlab.com/kaushalsingh1715/gitlab-zero-to-production` |
| Local repository | `C:\Users\ASPL-PUNE\gitlab-zero-to-production-gitlab` |
| Documentation repo | GitHub (used separately for learning notes) |

---

## 13. Practical 1 — Create GitLab Issue

| Field | Value |
|---|---|
| Title | `Create GitLab Issues and Project Management Documentation` |
| Number | `#1` |
| Assignee | `kaushal singh` |
| Label | `documentation` |

The Issue was successfully created in the GitLab project. ✅

---

## 14. Practical 2 — Create Feature Branch

Switch to `main`:

```bash
git switch main
```

Get the latest changes:

```bash
git pull origin main
```

Create the feature branch:

```bash
git switch -c feature/issue-1-lesson7
```

> 💡 Including the Issue number in the branch name (`issue-1`) makes it easy to see which Issue the branch belongs to.

---

## 15. Practical 3 — Create Practice Change

File created: `lesson7-issue-practice.txt`

Content:

```text
Lesson 7 - GitLab Issue #1 Practice
```

Verified locally:

```bash
type lesson7-issue-practice.txt      # Windows CMD
cat lesson7-issue-practice.txt       # Git Bash / macOS / Linux
```

---

## 16. Practical 4 — Commit the Change

```bash
git add lesson7-issue-practice.txt
git commit -m "Add Lesson 7 issue practice"
```

Resulting commit:

```text
bac1ab3 Add Lesson 7 issue practice
```

---

## 17. Practical 5 — Push Feature Branch

```bash
git push -u origin feature/issue-1-lesson7
```

GitLab created the remote branch `origin/feature/issue-1-lesson7`, and the local branch was configured to track it.

```bash
git status
```

```text
On branch feature/issue-1-lesson7
Your branch is up to date with 'origin/feature/issue-1-lesson7'.

nothing to commit, working tree clean
```

---

## 18. Practical 6 — Create Merge Request

| Field | Value |
|---|---|
| Source branch | `feature/issue-1-lesson7` |
| Target branch | `main` |
| Title | `Add Lesson 7 issue practice` |

Merge Request description:

```markdown
## Summary

- Added Lesson 7 issue practice file.
- Demonstrated the Issue → Branch → Commit → Merge Request workflow.

Closes #1
```

---

## 19. Closing Keywords: `Closes #1`

The keyword `Closes #1` in the MR description **links** the Merge Request to Issue #1.

```text
Issue #1
    │
    │  Closes #1
    v
Merge Request
    │
    v
main   →   Issue #1 closed automatically ✅
```

When the Merge Request is merged, GitLab **automatically closes** the referenced Issue.

Other closing keywords that work the same way:

```text
Close #1    Closes #1    Closed #1
Fix #1      Fixes #1     Fixed #1
Resolve #1  Resolves #1  Resolved #1
Implement #1  Implements #1
```

> ⚠️ **Note:** Auto-closing happens only when the MR is merged into the project's **default branch** (usually `main`). To link an Issue *without* closing it, use `Related to #1` instead.

This provides **traceability** between project management and source-code changes.

---

## 20. Complete Lesson 7 Workflow

```text
Create Issue #1
        │
        v
Assign Issue
        │
        v
Add documentation label
        │
        v
Create feature/issue-1-lesson7
        │
        v
Create practice file
        │
        v
Commit
        │
        v
Push branch
        │
        v
Create Merge Request
        │
        v
Link MR to Issue #1 (Closes #1)
        │
        v
Merge
        │
        v
Issue #1 closed automatically
```

---

# 📋 Summary

## 21. Important Git Commands

| Command | Purpose |
|---|---|
| `git switch main` | Switch to `main` |
| `git pull origin main` | Update local `main` |
| `git switch -c <branch>` | Create and switch to a new branch |
| `git add <file>` | Stage a file |
| `git commit -m "message"` | Create a commit |
| `git push -u origin <branch>` | Push a new branch and configure upstream |
| `git status` | Check repository state |
| `git branch` | List local branches |
| `git log --oneline` | View commit history |

---

## 22. Production DevOps Workflow

Issues become particularly useful when integrated with the development and CI/CD lifecycle:

```text
Business Requirement
        │
        v
GitLab Issue
        │
        v
Developer
        │
        v
Feature Branch
        │
        v
Code Changes → Commit
        │
        v
Merge Request
        ├── Code Review
        ├── Automated Tests
        └── Security Checks
        │
        v
Merge
        │
        v
Main Branch
        │
        v
CI/CD Pipeline
        │
        v
Build → Test → Package
        │
        v
Deployment
```

This provides a **traceable path** from a business requirement all the way to deployment.

---

## 23. Issue Management in a DevOps Environment

In a larger project, Issues can represent:

- Bugs
- Features
- Technical debt
- Infrastructure tasks
- Documentation
- Security tasks
- Performance improvements
- Deployment tasks

Example of related Issues:

```text
Issue #101  Improve API performance
        ↓
Issue #102  Add Redis caching
        ↓
Issue #103  Create monitoring dashboard
```

Each can then be implemented through its own feature branch and Merge Request.

---

## 24. Key Learnings

- [x] GitLab Issues
- [x] Issue titles
- [x] Issue descriptions
- [x] Issue labels
- [x] Issue assignees
- [x] Milestones
- [x] Issue lifecycle
- [x] Issue comments
- [x] Feature branches for Issues
- [x] Linking Issues and Merge Requests
- [x] Closing keywords (`Closes #1`)
- [x] Issue-to-code traceability
- [x] Feature branch workflow
- [x] Commit and push workflow
- [x] Merge Request workflow

---

## 25. Lesson 7 Outcome

A complete **Issue-driven development workflow** was practiced:

```text
Issue
  ↓
Feature Branch
  ↓
Code Change
  ↓
Commit
  ↓
Push
  ↓
Merge Request
  ↓
Issue Link
  ↓
Merge
```

This establishes the foundation for more advanced GitLab project-management and collaboration workflows.

---

### ✅ Status: Lesson 7 — Completed

