# Lesson 1 — Git & GitLab Fundamentals

## 🎯 Objective

The objective of this lesson was to understand the foundations of **version control**, the difference between **Git** and **GitLab**, the difference between **local and remote repositories**, and the basic **Git workflow**.

---

## 📚 Table of Contents

1. [What Is Version Control?](#1-what-is-version-control)
2. [What Is Git?](#2-what-is-git)
3. [What Is GitLab?](#3-what-is-gitlab)
4. [Git vs GitLab](#4-git-vs-gitlab)
5. [Local vs Remote Repository](#5-local-vs-remote-repository)
6. [Basic Git Workflow](#6-basic-git-workflow)
7. [Key Terms](#7-key-terms)
8. [Key Takeaways](#8-key-takeaways)

---

## 1. What Is Version Control?

Without version control, project folders often end up looking like this:

```text
project.docx
project_v2.docx
project_final.docx
project_final_REALLY_final.docx
project_final_fixed_by_rahul.docx
```

It becomes hard to know **what changed**, **who changed it**, **when**, and how to **go back**.

**Version control** solves this by recording every change to your files over time.

> 🎮 **Analogy:** Version control works like **save points in a video game**. You save before a risky step, and if something goes wrong, you reload the save point instead of starting over.

---

## 2. What Is Git?

**Git** is a **distributed version-control system** used to track changes to files and maintain the history of a project.

- It runs **on your own computer**.
- It works **offline**.
- *Distributed* means every developer has a **full copy** of the project history, not just the latest files.

---

## 3. What Is GitLab?

**GitLab** is a **DevSecOps platform** that provides Git repository hosting along with:

- Collaboration (Issues, Merge Requests, code review)
- CI/CD (automated build, test, and deploy)
- Security scanning
- Package and container registries
- Deployment capabilities

> 📸 **Analogy:** Git is the **camera** that takes snapshots of your code. GitLab is like **Google Photos** — it stores those snapshots safely online, lets others see and comment on them, and can automatically process them.

---

## 4. Git vs GitLab

| Git | GitLab |
|---|---|
| Version-control system | DevSecOps platform |
| Tracks changes | Hosts Git repositories |
| Works locally | Provides remote repository hosting |
| Uses commits and branches | Provides Merge Requests, CI/CD, security, and deployment |
| A command-line tool | A website / server application |

> 💡 GitLab **uses** Git — it doesn't replace it. GitHub and Bitbucket are other platforms that host Git repositories.

---

## 5. Local vs Remote Repository

| | Local Repository | Remote Repository |
|---|---|---|
| **Where** | On the developer's computer | Hosted on a platform such as GitLab or GitHub |
| **Used for** | Day-to-day work and commits | Sharing work, backup, collaboration, CI/CD |
| **Works offline** | ✅ | ❌ |

```text
Your Computer                         GitLab (remote)
┌──────────────────┐   git push →   ┌──────────────────┐
│ Local Repository │                │ Remote Repository│
└──────────────────┘   ← git pull   └──────────────────┘
```

---

## 6. Basic Git Workflow

```text
Working Directory
       │
       │  git add
       ▼
Staging Area
       │
       │  git commit
       ▼
Local Repository
       │
       │  git push
       ▼
Remote Repository
```

| Area | What it is | Command to move forward |
|---|---|---|
| **Working Directory** | The files you are currently editing | `git add` |
| **Staging Area** | Changes selected for the next commit | `git commit` |
| **Local Repository** | Saved history (commits) on your computer | `git push` |
| **Remote Repository** | Shared copy on GitLab | — |

> 📦 **Analogy:** The **staging area** is like a **packing box**. You choose which items go in (`git add`), seal and label the box (`git commit`), then ship it (`git push`).

Example:

```bash
git add README.md
git commit -m "Add project README"
git push origin main
```

---

## 7. Key Terms

| Term | Meaning |
|---|---|
| **Repository (repo)** | A project folder tracked by Git |
| **Commit** | A snapshot of changes with a message describing them |
| **Branch** | An independent line of development (Lesson 5) |
| **Remote** | A link to a repository hosted elsewhere, usually named `origin` |
| **Push** | Send local commits to the remote repository |
| **Pull** | Get commits from the remote repository and integrate them locally |

---

## 8. Key Takeaways

- [x] What version control is and why it's needed
- [x] What Git is
- [x] What GitLab is
- [x] The difference between Git and GitLab
- [x] Local vs remote repositories
- [x] The basic Git workflow: working directory → staging area → local repository → remote repository

---

### ✅ Status: Lesson 1 — Completed
