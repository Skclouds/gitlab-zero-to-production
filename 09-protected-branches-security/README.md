# Lesson 9 — GitLab Protected Branches & Repository Security

## 🎯 Objective

The objective of this lesson was to understand **protected branches** — a key GitLab security feature — and how they work together with GitLab roles and the Merge Request workflow to prevent unauthorized or accidental changes to important branches.

---

## 📚 Table of Contents

**Concepts**

1. [Introduction](#1-introduction)
2. [Protected Branch — Layman Explanation](#2-protected-branch--layman-explanation)
3. [Why Protect `main`?](#3-why-protect-main)
4. [Normal Branch vs Protected Branch](#4-normal-branch-vs-protected-branch)
5. [Production Example](#5-production-example)
6. [GitLab Protected Branch Configuration](#6-gitlab-protected-branch-configuration)
7. [Our `main` Branch Configuration](#7-our-main-branch-configuration)
8. [Meaning of Each Setting](#8-meaning-of-each-setting)
9. [Lesson 8 Connection — Roles and Protected Branches](#9-lesson-8-connection--roles-and-protected-branches)

**Hands-On**

10. [Step 1 — Check the Repository](#10-step-1--check-the-repository)
11. [Step 2 — Create a Test Branch](#11-step-2--create-a-test-branch)
12. [Step 3 — Create a Test File](#12-step-3--create-a-test-file)
13. [Step 4 — Stage and Commit the File](#13-step-4--stage-and-commit-the-file)
14. [Step 5 — Push the Test Branch](#14-step-5--push-the-test-branch)
15. [Step 6 — Direct Push Test](#15-step-6--direct-push-test)
16. [Git Protection vs GitLab Protection](#16-git-protection-vs-gitlab-protection)
17. [How to Properly Test GitLab Branch Protection](#17-how-to-properly-test-gitlab-branch-protection)
18. [Clean Up the Test Branch](#18-clean-up-the-test-branch)

**Production & Security**

19. [Safe Production Workflow](#19-safe-production-workflow)
20. [Why This Matters in DevOps](#20-why-this-matters-in-devops)
21. [Security Principles Learned](#21-security-principles-learned)

**Summary**

22. [Important Commands](#22-important-commands)
23. [Key Takeaways](#23-key-takeaways)
24. [Lesson 9 Completion](#24-lesson-9-completion)

---

## 1. Introduction

**Protected branches** are an important GitLab security feature used to protect important branches such as:

- `main`
- `master`
- `develop`
- Release branches
- Production branches

> 🎯 The main purpose of a protected branch is to **prevent unauthorized or accidental changes**.

In a production environment, developers normally work on **feature branches** and use **Merge Requests** to move their changes into protected branches.

---

## 2. Protected Branch — Layman Explanation

Think of a GitLab repository like a company:

```text
Company
│
├── Developer Area
│     └── Feature Branches
│
└── Production Area 🔒
      └── main
```

Developers can work freely in their own area. But the production area is **protected** — they can't simply walk in and change production directly.

Instead:

```text
Developer
    ↓
Feature Branch
    ↓
Commit
    ↓
Push
    ↓
Merge Request
    ↓
Review
    ↓
Approval / CI checks
    ↓
main 🔒
```

This is the basic idea behind protected branches.

---

## 3. Why Protect `main`?

The `main` branch commonly represents the **stable version** of the project.

Without protection, someone could accidentally run:

```bash
git push origin main
```

and directly change the branch.

In a production workflow, changes should go through a **controlled process**:

```text
Feature Branch
      ↓
Merge Request
      ↓
Code Review
      ↓
CI/CD Checks
      ↓
Protected main 🔒
```

---

## 4. Normal Branch vs Protected Branch

| | Normal Branch | Protected Branch |
|---|---|---|
| **Examples** | `feature/login`, `feature/payment`, `feature/user-profile` | `main` 🔒 |
| **Direct push** | ✅ Allowed for Developers | ❌ Restricted (can be set to *No one*) |
| **How changes arrive** | Developer pushes directly | Through a Merge Request |
| **Force push** | Usually allowed | Usually disabled |

Normal branch workflow:

```bash
git add .
git commit -m "Add login feature"
git push origin feature/login
```

For a protected branch, developers create a **Merge Request** instead of pushing directly.

---

## 5. Production Example

Imagine a company has the repository `ecommerce-application`, with `main` as its production branch. A developer wants to add a payment feature.

Instead of changing `main` directly:

```text
Developer
   │
   ▼
feature/payment
   │
   ├── Code
   ├── Commit
   └── Push
         │
         ▼
   Merge Request
         │
         ▼
   Code Review
         │
         ▼
       CI/CD
         │
         ▼
     main 🔒
```

This makes the workflow **controlled and auditable**.

---

## 6. GitLab Protected Branch Configuration

Protected branches are configured from:

```text
Project
   ↓
Settings
   ↓
Repository
   ↓
Protected branches
```

GitLab also provides **Branch rules**, which bring branch protection, approval rules, and status checks together in one place.

---

## 7. Our `main` Branch Configuration

For this learning project, `main` was configured as a protected branch:

| Setting | Value |
|---|---|
| Branch | `main` |
| Allowed to merge | Maintainers |
| Allowed to push and merge | No one |
| Allowed to force push | ❌ OFF |

```text
main 🔒

Merge       → Maintainers
Direct Push → No one
Force Push  → Disabled
```

---

## 8. Meaning of Each Setting

### Allowed to merge

Controls **who can merge Merge Requests** into the protected branch.

```text
Allowed to merge → Maintainers
```

> 💡 Selecting **Maintainers** also includes roles above it — so **Owners** can merge too.

### Allowed to push and merge

Controls **who can push directly** to the protected branch.

```text
Allowed to push and merge → No one
```

This is important because it **forces the Merge Request workflow** — nobody, not even the Owner, can bypass review by pushing straight to `main`.

```text
Developer
    ↓
Feature Branch
    ↓
Merge Request
    ↓
Maintainer
    ↓
main
```

### Allowed to force push

Force pushing **rewrites branch history**:

```bash
git push --force
```

On an important branch, this is dangerous because commits can be rewritten or removed.

```text
Allowed to force push → OFF
```

---

## 9. Lesson 8 Connection — Roles and Protected Branches

Protected branches are closely related to GitLab roles. In Lesson 8, we learned that roles have different levels of access:

```text
Guest → Planner → Reporter → Developer → Maintainer → Owner
```

A **Developer** can work with feature branches and create Merge Requests. A **protected branch** then restricts who is allowed to merge or push to important branches.

```text
GitLab Role
     +
Branch Protection
     ↓
Access Control
```

This is an important DevOps security concept.

---

# 🛠️ Hands-On

| Item | Value |
|---|---|
| GitLab repository | `gitlab-zero-to-production` |
| Local repository | `C:\Users\ASPL-PUNE\gitlab-zero-to-production-gitlab` |

## 10. Step 1 — Check the Repository

```bash
git status
git branch --show-current
git log --oneline -3
```

---

## 11. Step 2 — Create a Test Branch

```bash
git switch -c test/protected-main
```

The current branch became:

```text
test/protected-main
```

---

## 12. Step 3 — Create a Test File

```bash
echo Protected branch test - Lesson 9 > lesson9-protection-test.txt
git status
```

The file initially appeared as an **untracked** file.

---

## 13. Step 4 — Stage and Commit the File

```bash
git add lesson9-protection-test.txt
git commit -m "Test protected main branch"
```

Resulting commit:

```text
04cfaa7 Test protected main branch
```

---

## 14. Step 5 — Push the Test Branch

```bash
git push -u origin test/protected-main
```

✅ **This succeeded** — because `test/protected-main` is **not** a protected branch, so a normal push was allowed.

---

## 15. Step 6 — Direct Push Test

The following command was used to test a direct push to `main`:

```bash
git push origin HEAD:main
```

The push was **rejected**:

```text
! [rejected]        HEAD -> main (fetch first)
```

Git also reported:

```text
remote contains work that you do not have locally
```

### ⚠️ Important Learning

This rejection was caused by **Git's branch-history (fast-forward) check** — the remote `main` had commits that the local branch didn't have.

It was **not** proof that GitLab's protected-branch rule rejected the push. Git stopped the push before GitLab's permission check was even reached.

---

## 16. Git Protection vs GitLab Protection

There are **two separate mechanisms**:

| | Git (history check) | GitLab (permission check) |
|---|---|---|
| **What it checks** | Does the remote have commits you don't have locally? | Is the branch protected, and is this user allowed to push? |
| **Where it happens** | Git itself (fast-forward rule) | GitLab server (protected branch rule) |
| **Typical error** | `! [rejected] HEAD -> main (fetch first)` | `! [remote rejected] HEAD -> main (pre-receive hook declined)` |
| **Fix** | `git fetch` / `git pull` first | Use a Merge Request |

GitLab's check, conceptually:

```text
Local Git
   ↓
Push
   ↓
GitLab
   ↓
Is branch protected?  → YES
   ↓
Is user allowed to push?  → NO
   ↓
Reject
```

> 💡 A `fetch first` rejection should **not** be interpreted as a protected-branch rejection.

---

## 17. How to Properly Test GitLab Branch Protection

To see GitLab's protection actually block the push, first remove the Git history problem by building the test commit **on top of the latest `main`**:

```bash
git fetch origin
git rebase origin/main
git push origin HEAD:main
```

Now Git's fast-forward check passes, so the push reaches GitLab's permission check. Because *Allowed to push and merge* is set to **No one**, the expected result is:

```text
remote: GitLab: You are not allowed to push code to protected branches on this project.
 ! [remote rejected] HEAD -> main (pre-receive hook declined)
```

| Keyword in the error | Who rejected the push |
|---|---|
| `fetch first` / `non-fast-forward` | **Git** (history) |
| `remote rejected` / `pre-receive hook declined` / `protected branches` | **GitLab** (permissions) ✅ |

---

## 18. Clean Up the Test Branch

After testing, remove the temporary branch:

```bash
git switch main
git branch -D test/protected-main
git push origin --delete test/protected-main
git fetch --prune
```

> ℹ️ `-D` (force delete) is needed because the test commit was never merged into `main`.

---

# 🔐 Production & Security

## 19. Safe Production Workflow

```text
                  GitLab Repository
                         │
                         ▼
                     main 🔒
                         ▲
                         │
                   Merge Request
                         ▲
                         │
                  feature branch
                         ▲
                         │
                     Developer
```

Detailed workflow:

```text
1. Create feature branch
        ↓
2. Develop code
        ↓
3. Commit changes
        ↓
4. Push feature branch
        ↓
5. Create Merge Request
        ↓
6. Review changes
        ↓
7. Run CI/CD checks
        ↓
8. Maintainer merges
        ↓
9. main 🔒
```

---

## 20. Why This Matters in DevOps

Protected branches become especially important when GitLab is connected with CI/CD:

```text
Developer
    ↓
GitLab Feature Branch
    ↓
Merge Request
    ↓
Jenkins / GitLab CI
    ↓
Unit Tests
    ↓
SonarQube
    ↓
Security Checks
    ↓
Approval
    ↓
Protected main
    ↓
Build Artifact
    ↓
JFrog Artifactory
    ↓
Deployment
```

This prevents an uncontrolled change from easily reaching production.

---

## 21. Security Principles Learned

### Principle 1 — Protect important branches

```text
main 🔒
production 🔒
release/* 🔒
```

> 💡 Wildcards like `release/*` protect every branch matching the pattern.

### Principle 2 — Avoid unnecessary direct pushes

```text
Feature Branch → Merge Request → Review → Merge
```

### Principle 3 — Disable force pushes on important branches

Force pushes can rewrite branch history.

```text
Force Push → OFF
```

### Principle 4 — Use role-based access

Give users only the permissions they need — the **Principle of Least Privilege**.

---

# 📋 Summary

## 22. Important Commands

| Command | Purpose |
|---|---|
| `git branch --show-current` | Check current branch |
| `git status` | Check repository state |
| `git switch -c <branch>` | Create and switch to a branch |
| `git push -u origin <branch>` | Push a branch and set upstream |
| `git fetch origin` | Fetch remote information |
| `git rebase origin/main` | Replay your commits on top of the latest `main` |
| `git log --oneline -3` | View recent commits |
| `git push origin HEAD:main` | Attempt a direct push to `main` (testing only) |
| `git push origin --delete <branch>` | Delete a remote branch |

---

## 23. Key Takeaways

After completing Lesson 9, I understand:

- [x] What a protected branch is
- [x] Why `main` should be protected
- [x] The difference between a normal branch and a protected branch
- [x] How protected branches support the Merge Request workflow
- [x] The meaning of **Allowed to merge**
- [x] The meaning of **Allowed to push and merge**
- [x] Why force push should be disabled on important branches
- [x] How GitLab roles and branch protection work together
- [x] How to configure protection for `main`
- [x] The difference between Git's history (fast-forward) rejection and GitLab's protected-branch permission rejection
- [x] Why protected branches are important in production DevOps workflows

---

## 24. Lesson 9 Completion

| Item | Status |
|---|---|
| Theory | ✅ |
| Layman explanation | ✅ |
| Production example | ✅ |
| `main` protection configured | ✅ |
| Test branch | ✅ |
| Commit practice | ✅ |
| Push practice | ✅ |
| Git vs GitLab rejection understood | ✅ |
| Security concepts | ✅ |

---

### ✅ Status: Lesson 9 — Completed

---

⬅️ **Previous:** Lesson 8 — GitLab Users, Groups & Permissions | ➡️ **Next:** Lesson 10
