# Lesson 5 — Git Branching

## 🎯 Objective

The objective of this lesson is to understand **Git branching** and how branches are used to create separate lines of development.

Branching is an essential Git concept because it enables developers to work on features, bug fixes, and other changes **without directly modifying the main development branch**.

---

## 📚 Table of Contents

1. [What Is a Git Branch?](#1-what-is-a-git-branch)
2. [Why Do We Need Branches?](#2-why-do-we-need-branches)
3. [The `main` Branch](#3-the-main-branch)
4. [Creating a Branch](#4-creating-a-branch)
5. [Switching to a Branch](#5-switching-to-a-branch)
6. [Creating and Switching to a Branch](#6-creating-and-switching-to-a-branch)
7. [Understanding `HEAD`](#7-understanding-head)
8. [Branches and Commits](#8-branches-and-commits)
9. [Checking the Current Branch](#9-checking-the-current-branch)
10. [Local Branch vs Remote Branch](#10-local-branch-vs-remote-branch)
11. [Pushing a New Branch](#11-pushing-a-new-branch)
12. [Understanding `-u`](#12-understanding--u)
13. [Viewing Remote Branches](#13-viewing-remote-branches)
14. [Viewing All Branches](#14-viewing-all-branches)
15. [Deleting a Local Branch](#15-deleting-a-local-branch)
16. [Deleting a Remote Branch](#16-deleting-a-remote-branch)
17. [Branch Naming Conventions](#17-branch-naming-conventions)
18. [Real-World DevOps Branching Workflow](#18-real-world-devops-branching-workflow)
19. [Lesson 5 Hands-On](#19-lesson-5-hands-on)
20. [Important Git Branching Commands](#20-important-git-branching-commands)
21. [Key Takeaways](#21-key-takeaways)
22. [Lesson 5 Status](#22-lesson-5-status)

---

## 1. What Is a Git Branch?

A Git branch is an **independent line of development** in a Git repository.

```text
main
 |
 A --- B --- C
```

If a developer wants to work on a new feature, they can create a separate branch:

```text
A --- B --- C          <- main
             \
              D --- E  <- feature/login
```

The `main` branch continues to represent the main development line, while `feature/login` is used for developing the login functionality.

> **Simple Definition:** A branch is a separate line of development that allows developers to work on changes without directly affecting another branch.

---

## 2. Why Do We Need Branches?

Imagine multiple developers working directly on the `main` branch:

```text
Developer A ──┐
Developer B ──┤
Developer C ──┼──> main
Developer D ──┤
Developer E ──┘
```

This can result in:

- Conflicting changes
- Unstable code
- Accidental changes to the main codebase
- Difficult code reviews
- Difficult troubleshooting
- Difficult collaboration

With branches, developers can work independently:

```text
Developer A ───> feature/login ────────┐
                                       │
Developer B ───> feature/payment ──────┼──> main
                                       │
Developer C ───> bugfix/login-error ───┘
```

Each developer can work on a specific task without directly modifying the `main` branch.

---

## 3. The `main` Branch

`main` is commonly used as the **primary branch** of a Git repository.

```text
main
 |
 A --- B --- C
```

In a production-oriented workflow, the `main` branch is often **protected** so that changes are reviewed and validated before they are merged.

> 💡 Protected branches and Merge Requests will be covered in more detail in later lessons.

---

## 4. Creating a Branch

A branch can be created using:

```bash
git branch feature-login
```

> ⚠️ This **creates** the branch but does **not** switch to it.

To view the branches:

```bash
git branch
```

Example output:

```text
* main
  feature-login
```

The `*` indicates the branch that is currently checked out.

---

## 5. Switching to a Branch

The `git switch` command is used to switch to an existing branch:

```bash
git switch feature-login
```

Verify the current branch:

```bash
git branch
```

Example output:

```text
  main
* feature-login
```

The `*` has moved to `feature-login`.

---

## 6. Creating and Switching to a Branch

Instead of using two commands:

```bash
git branch feature-login
git switch feature-login
```

we can create and switch to a branch using a single command:

```bash
git switch -c feature-login
```

The `-c` option means: **create a new branch and switch to it.**

> ✅ This is the command used during the Lesson 5 hands-on.

---

## 7. Understanding `HEAD`

`HEAD` is an important concept in Git.

> **Simple way to understand it:** `HEAD` represents your **current position** in the Git repository.

When working on a branch, `HEAD` normally points to the current branch.

```text
HEAD
 |
 v
main
 |
 v
Commit C
```

After switching to another branch:

```text
HEAD
 |
 v
feature-login
 |
 v
Commit C
```

Understanding `HEAD` becomes important when working with branches, commits, resets, merges, and other advanced Git operations.

---

## 8. Branches and Commits

Suppose the repository contains:

```text
A --- B --- C   <- main
```

A new branch is created. Both branches point to the same commit:

```text
A --- B --- C   <- main
                <- feature-login
```

After making a change and creating a commit on the feature branch:

```text
A --- B --- C         <- main
             \
              D       <- feature-login
```

The `main` branch remains at commit `C`, while the feature branch moves to commit `D`.

This allows development to continue independently.

---

## 9. Checking the Current Branch

| Command | What it shows |
|---|---|
| `git branch` | Lists local branches (current one marked with `*`) |
| `git branch --show-current` | Prints only the current branch name |
| `git status` | Shows the current branch near the top of the output |

Example `git status` output:

```text
On branch feature/login
```

---

## 10. Local Branch vs Remote Branch

### Local Branch

A branch that exists in the **local repository** on the developer's computer.

```text
Your Computer
    |
    +--- main
    |
    +--- feature/login
```

### Remote Branch

A branch available on the **remote repository**.

```text
GitHub / GitLab
    |
    +--- main
    |
    +--- feature/login
```

> ⚠️ A local branch does **not** automatically exist on the remote repository. It must be **pushed** first.

---

## 11. Pushing a New Branch

Create a branch:

```bash
git switch -c feature/login
```

After making changes:

```bash
git add .
git commit -m "Add login feature"
```

Push the branch:

```bash
git push -u origin feature/login
```

Conceptually:

```text
Local Repository
       |
       | git push -u
       v
Remote Repository
       |
       v
feature/login
```

---

## 12. Understanding `-u`

```bash
git push -u origin feature/login
```

| Part | Meaning |
|---|---|
| `git push` | Send commits to a remote repository |
| `-u` | Establish an **upstream** (tracking) relationship between the local and remote branch |
| `origin` | The name of the configured remote repository |
| `feature/login` | The branch being pushed |

After the upstream relationship is established, future pushes can simply use:

```bash
git push
```

instead of specifying the remote and branch every time.

---

## 13. Viewing Remote Branches

```bash
git branch -r
```

Example output:

```text
origin/main
origin/feature/login
```

- `origin/main` — the remote-tracking reference for the `main` branch on the remote named `origin`.
- `origin/feature/login` — the remote-tracking reference for `feature/login`.

---

## 14. Viewing All Branches

To view both local and remote branches:

```bash
git branch -a
```

Example output:

```text
* feature/login
  main
  remotes/origin/main
  remotes/origin/feature/login
```

This command provides a broader view of the branch structure.

---

## 15. Deleting a Local Branch

Safe delete:

```bash
git branch -d feature/login
```

The `-d` option performs a normal/safe deletion. Git may prevent the deletion if the branch contains changes that have not been merged.

Force delete:

```bash
git branch -D feature/login
```

> ⚠️ Use `-D` carefully — it deletes a branch even when Git considers it unmerged, and those commits may be lost.

---

## 16. Deleting a Remote Branch

```bash
git push origin --delete feature/login
```

This removes the branch from the remote repository.

```text
Local Git
    |
    | delete remote branch
    v
Remote Repository
```

---

## 17. Branch Naming Conventions

Branch names should clearly communicate the **purpose** of the branch.

| Type | Examples |
|---|---|
| **Feature** | `feature/login`, `feature/payment`, `feature/user-registration` |
| **Bug Fix** | `bugfix/login-error`, `bugfix/database-connection` |
| **Hotfix** | `hotfix/production-error` |
| **Release** | `release/v1.0.0` |

❌ Avoid unclear names such as:

```text
test
new
abc
mybranch
final
final2
new-final
```

Meaningful branch names make it easier for development teams to understand the purpose of each branch.

---

## 18. Real-World DevOps Branching Workflow

A typical GitLab-based development workflow:

```text
                     GitLab
                        |
                       main
                        |
              +---------+---------+
              |                   |
              v                   v
        feature/login       feature/payment
              |                   |
              v                   v
         Development         Development
              |                   |
              +---------+---------+
                        |
                        v
                  Merge Request
                        |
                        v
                     Review
                        |
                        v
                   CI/CD Tests
                        |
                        v
                  Merge to main
```

The developer works on a **feature branch** rather than directly modifying `main`.

General workflow:

```text
Create Branch → Develop → Commit → Push → Create Merge Request → Code Review → CI/CD Validation → Merge
```

> 💡 Merge Requests will be covered in **Lesson 6**.

---

## 19. Lesson 5 Hands-On

The branching practical was performed using the repository:

🔗 https://github.com/Skclouds/gitlab-zero-to-production

### 19.0 Verify the Starting Point

```bash
git branch --show-current
```

```text
main
```

```bash
git status
```

```text
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

### 19.1 Create the Feature Branch

```bash
git switch -c feature/lesson5-branching
```

```text
Switched to a new branch 'feature/lesson5-branching'
```

Verify:

```bash
git branch
```

```text
* feature/lesson5-branching
  main
```

```bash
git branch --show-current
```

```text
feature/lesson5-branching
```

### 19.2 Create a Practice File

```bash
echo Lesson 5 - Git Branching Practice>lesson5.txt
```

Git initially identified the file as untracked:

```text
Untracked files:
        lesson5.txt
```

### 19.3 Stage the File

```bash
git add lesson5.txt
git status
```

```text
Changes to be committed:
        new file:   lesson5.txt
```

### 19.4 Commit the Change

```bash
git commit -m "Add Lesson 5 branching practice"
```

Resulting commit:

```text
dd1f82d Add Lesson 5 branching practice
```

### 19.5 Push the Branch

```bash
git push -u origin feature/lesson5-branching
```

The remote confirmed the new branch:

```text
* [new branch]      feature/lesson5-branching -> feature/lesson5-branching
```

The local branch was configured to track `origin/feature/lesson5-branching`.

### 19.6 Verify Remote Branches

```bash
git branch -r
```

```text
origin/feature/lesson5-branching
origin/main
```

```bash
git branch -a
```

```text
* feature/lesson5-branching
  main
  remotes/origin/feature/lesson5-branching
  remotes/origin/main
```

✅ This confirmed that the feature branch exists **both locally and on the remote repository**.

---

## 20. Important Git Branching Commands

| Command | Purpose |
|---|---|
| `git branch` | Lists local branches |
| `git branch <name>` | Creates a branch |
| `git switch <name>` | Switches to an existing branch |
| `git switch -c <name>` | Creates and switches to a new branch |
| `git branch --show-current` | Displays the current branch |
| `git branch -r` | Lists remote branches |
| `git branch -a` | Lists local and remote branches |
| `git push -u origin <branch>` | Pushes a new branch and establishes upstream tracking |
| `git branch -d <branch>` | Safely deletes a local branch |
| `git branch -D <branch>` | Force-deletes a local branch |
| `git push origin --delete <branch>` | Deletes a remote branch |

---

## 21. Key Takeaways

After completing this lesson, the following concepts should be understood:

- A Git branch represents a separate line of development.
- Branches allow developers to work independently.
- `main` is commonly used as the primary branch.
- `git branch` creates a branch **without** switching to it.
- `git switch` changes the current branch.
- `git switch -c` creates **and** switches to a new branch.
- `HEAD` represents the current position in the Git repository.
- Local branches exist in the local repository; remote branches exist on the remote repository.
- A local branch must be pushed before it becomes available on the remote repository.
- `git push -u` establishes upstream tracking.
- Branch naming conventions help teams understand the purpose of branches.
- Feature branches are commonly used before creating Merge Requests.
- Branches form the foundation of collaborative GitLab development workflows.

---

## 22. Lesson 5 Status

### Concepts

- [x] What is a Git Branch?
- [x] Why Branches Are Used
- [x] The `main` Branch
- [x] Creating Branches
- [x] Switching Branches
- [x] Creating and Switching to a Branch
- [x] Understanding `HEAD`
- [x] Local vs Remote Branches
- [x] Pushing Branches
- [x] Upstream Tracking
- [x] Viewing Branches
- [x] Deleting Branches
- [x] Branch Naming Conventions
- [x] Real-World DevOps Branching Workflow

### Hands-On

- [x] Created `feature/lesson5-branching`
- [x] Created `lesson5.txt`
- [x] Staged the file
- [x] Created a commit
- [x] Pushed the feature branch
- [x] Verified the remote branch

---

⬅️ **Previous:** Lesson 4 | ➡️ **Next:** Lesson 6 — Merge Requests
