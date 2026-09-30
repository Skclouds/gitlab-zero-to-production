# Lesson 8 — GitLab Users, Groups & Permissions

## 🎯 Objective

The objective of this lesson was to understand **GitLab access control** and how users, groups, subgroups, project membership, and roles are organized.

The lesson focused on:

- GitLab users
- Project membership
- Groups
- Subgroups
- Project-level access
- Group-level access
- Roles
- Least privilege
- Temporary access
- Permission inheritance concepts
- Protected branches and permissions
- Practical GitLab access-management exploration

Hands-on work was performed using the actual GitLab project and a **separate practice group**.

---

## 📚 Table of Contents

**Concepts**

1. [What Are GitLab Permissions?](#1-what-are-gitlab-permissions)
2. [Real-Life Analogy](#2-real-life-analogy)
3. [GitLab User](#3-gitlab-user)
4. [Project Membership](#4-project-membership)
5. [Groups](#5-groups)
6. [Why Groups Are Important](#6-why-groups-are-important)
7. [Subgroups](#7-subgroups)
8. [GitLab Roles](#8-gitlab-roles)
9. [Guest](#9-guest)
10. [Planner](#10-planner)
11. [Reporter](#11-reporter)
12. [Security Manager](#12-security-manager)
13. [Developer](#13-developer)
14. [Maintainer and Owner](#14-maintainer-and-owner)
15. [Principle of Least Privilege](#15-principle-of-least-privilege)
16. [Temporary Access](#16-temporary-access)
17. [Project-Level vs Group-Level Access](#17-project-level-vs-group-level-access)
18. [Permission Hierarchy and Inheritance](#18-permission-hierarchy-and-inheritance)
19. [Protected Branch Connection](#19-protected-branch-connection)

**Hands-On**

20. [Hands-On Environment](#20-hands-on-environment)
21. [Practical 1 — Inspect Project Members](#21-practical-1--inspect-project-members)
22. [Practical 2 — Inspect Member Invitation](#22-practical-2--inspect-member-invitation)
23. [Practical 3 — Explore GitLab Roles](#23-practical-3--explore-gitlab-roles)
24. [Practical 4 — Create Practice Group](#24-practical-4--create-practice-group)
25. [Practical 5 — Create Subgroup](#25-practical-5--create-subgroup)
26. [Practice Environment](#26-practice-environment)

**Real-World Application**

27. [Example Enterprise Structure](#27-example-enterprise-structure)
28. [Access-Control Example](#28-access-control-example)
29. [Why Developers Should Not Automatically Have Administrative Access](#29-why-developers-should-not-automatically-have-administrative-access)
30. [Connection With Merge Requests](#30-connection-with-merge-requests)
31. [Connection With CI/CD](#31-connection-with-cicd)

**Summary**

32. [Key Learnings](#32-key-learnings)
33. [Lesson 8 Architecture](#33-lesson-8-architecture)
34. [Lesson 8 Outcome](#34-lesson-8-outcome)

---

## 1. What Are GitLab Permissions?

GitLab permissions determine **what actions a user is allowed to perform** within a GitLab project or group.

Different users receive different levels of access depending on their responsibilities. For example, a developer needs to:

```text
Developer
    ↓
Develop code
    ↓
Create branches
    ↓
Create Merge Requests
```

while a project administrator may require additional project-management permissions.

> 🎯 **Goal:** Provide the access required for the user's responsibility — **without** unnecessarily granting higher privileges.

---

## 2. Real-Life Analogy

GitLab permissions can be compared to access control in an office building:

| Office | Access |
|---|---|
| Visitor | Lobby only |
| Employee | Normal working areas |
| Manager | Additional management areas |
| Administrator | Server room, all keys |

GitLab works the same way:

```text
GitLab User
      ↓
Membership
      ↓
Role
      ↓
Permissions
```

---

## 3. GitLab User

A **GitLab user** is an individual GitLab account.

A user can participate in projects, groups, and subgroups — and can have **different access levels in each**.

```text
User
  │
  ├── Project A   (Developer)
  ├── Project B   (Reporter)
  └── Group       (Maintainer)
```

---

## 4. Project Membership

A user can be **directly added** to a GitLab project:

```text
User
  ↓
Project
  ↓
Role
```

Example:

```text
Developer → Application Project → Developer role
```

Project membership applies **only to that specific project**.

---

## 5. Groups

A **GitLab Group** organizes projects and users together.

```text
Company
│
├── Backend
│   ├── payment-api
│   └── user-api
│
├── Frontend
│   └── web-application
│
└── DevOps
    ├── infrastructure
    └── deployment
```

Groups are useful for organizing projects and managing access across **multiple projects at once**.

---

## 6. Why Groups Are Important

Without groups, access becomes hard to manage as projects and users grow. Imagine adding each user to each project one by one:

```text
User
 ├── Project A
 ├── Project B
 ├── Project C
 └── Project D
```

With a group, you add the user **once** and they get access to everything inside:

```text
DevOps Group  ← user added here once
 ├── Project A
 ├── Project B
 ├── Project C
 └── Project D
```

---

## 7. Subgroups

GitLab groups can contain **subgroups**:

```text
Company
│
├── Engineering
│   ├── Backend
│   ├── Frontend
│   └── DevOps
│
└── Security
    ├── Application Security
    └── Cloud Security
```

The hierarchy is:

```text
Parent Group
      ↓
Subgroup
      ↓
Project
```

Subgroups are useful for organizing larger GitLab environments.

---

## 8. GitLab Roles

GitLab provides roles with different levels of capability. During the hands-on exercise, the role selector displayed:

```text
Guest
Planner
Reporter
Security Manager
Developer
```

The standard GitLab roles, from least to most access, are:

```text
Guest → Planner → Reporter → Developer → Maintainer → Owner
```

> ⚠️ Available roles and their descriptions can vary by GitLab version, tier, and instance. Always check the role descriptions in your own GitLab instance.

---

## 9. Guest

GitLab's description:

> No code access. View and comment on issues and epics.

```text
Guest
  ↓
Limited project/work-item interaction
  ↓
No normal code access
```

---

## 10. Planner

GitLab's description:

> View code only. Create and manage issues, epics, milestones, and iterations.

```text
Planner
   ↓
Project planning
   ├── Issues
   ├── Epics
   ├── Milestones
   └── Iterations
```

This role shows that **project-management responsibilities can be separated** from software-development responsibilities.

---

## 11. Reporter

GitLab's description:

> View code only. Create issues and generate reports.

```text
Reporter
   ↓
View project information
   ├── Issues
   └── Reports
```

---

## 12. Security Manager

GitLab's description:

> View and manage security features for the group or project.

```text
Security Manager
        ↓
Security features
        ↓
Group / Project
```

This role is particularly relevant to **DevSecOps** workflows.

---

## 13. Developer

GitLab's description — a Developer can:

- ✅ Push code to **non-protected** branches
- ✅ Create merge requests
- ✅ Run pipelines
- ❌ Cannot manage project settings

```text
Developer
    ↓
Development activities
    ├── Feature branches
    ├── Merge Requests
    └── Pipelines
```

Project administration remains restricted.

---

## 14. Maintainer and Owner

These higher roles were not part of the visible dropdown list during the exercise, but they are standard GitLab roles:

| Role | Typical responsibilities |
|---|---|
| **Maintainer** | Manage project settings, protected branches, merge into protected branches, manage CI/CD settings and members |
| **Owner** | Full control, including deleting or transferring the project/group and managing everything a Maintainer can |

> 💡 In your own project, you are the **Owner** (see [Practical 1](#21-practical-1--inspect-project-members)).

---

## 15. Principle of Least Privilege

One of the most important security concepts in GitLab administration:

> 🔐 **Give a user only the permissions required to perform their responsibilities.**

```text
Developer              → Developer-level access
Project administrator  → Maintainer/Owner access
Security team member   → Security-related access
```

Users should **not** automatically receive the highest possible permissions.

---

## 16. Temporary Access

The **Invite Members** interface provides a **Grant temporary access** option (an expiration date).

```text
External Developer
       ↓
Developer role
       ↓
Temporary access
       ↓
Expiration date
       ↓
Access ends automatically
```

This is useful for contractors, auditors, or short-term collaborators, and reduces unnecessary long-term access.

---

## 17. Project-Level vs Group-Level Access

| | Project-Level | Group-Level |
|---|---|---|
| **Where the user is added** | A single project | A group |
| **What they get access to** | Only that project | All subgroups and projects inside the group |
| **Best for** | One-off access to a specific project | Teams working across many projects |

```text
Project-Level:              Group-Level:

User                        User
  ↓                           ↓
Specific Project            Group
  ↓                           ↓
Role                        Subgroup
                              ↓
                            Projects
```

---

## 18. Permission Hierarchy and Inheritance

A simplified organizational model:

```text
User
  ↓
Group
  ↓
Subgroup
  ↓
Project
  ↓
Role
  ↓
Permissions
```

**Inheritance:** When a user is added to a group, they **automatically inherit** that role in every subgroup and project inside it.

```text
GitLab Learning   ← User added as Developer
└── DevOps        ← User is automatically Developer here too
    └── project   ← ...and here
```

> 💡 A member's access can be **raised** at a lower level (e.g. Maintainer on one specific project), but inherited access can't be reduced there. To give *less* access, add the user at a lower level instead of at the parent group.

When managing access, always consider the **whole hierarchy**, not just an individual project.

---

## 19. Protected Branch Connection

Permissions become especially important when combined with **protected branches**. The Developer role specifically says developers can push to **non-protected** branches.

This creates a workflow such as:

```text
Developer
    ↓
Feature Branch
    ↓
Commit
    ↓
Merge Request
    ↓
Review
    ↓
Protected main
```

> 💡 Protected branches are covered in detail in **Lesson 9**.

---

# 🛠️ Hands-On

## 20. Hands-On Environment

| Item | Value |
|---|---|
| Main GitLab project | `gitlab-zero-to-production` |
| Local repository | `C:\Users\ASPL-PUNE\gitlab-zero-to-production-gitlab` |
| Documentation repo | https://github.com/Skclouds/gitlab-zero-to-production |

---

## 21. Practical 1 — Inspect Project Members

The project **Members** page was opened. The project had one direct member:

| Name | Username | Role |
|---|---|---|
| kaushal singh | `@kaushalsingh1715` | **Owner** |

```text
gitlab-zero-to-production
        │
        └── kaushal singh
                │
                └── Owner
```

---

## 22. Practical 2 — Inspect Member Invitation

The **Invite members** dialog was opened. It provided:

- Email addresses or GitLab usernames
- Role
- Grant temporary access

> ℹ️ No invitation was sent. This practical was used to understand how an administrator assigns project membership and roles.

---

## 23. Practical 3 — Explore GitLab Roles

The **Role** dropdown was inspected. Visible roles included:

```text
Guest
Planner
Reporter
Security Manager
Developer
```

The descriptions were examined to understand the difference between **collaboration, planning, reporting, security, and development** responsibilities.

> ℹ️ No external user was invited during this exercise.

---

## 24. Practical 4 — Create Practice Group

A separate practice group was created so access-control concepts could be explored **without changing the main learning project**.

| Setting | Value |
|---|---|
| Group name | `GitLab Learning` |
| Visibility | Private |
| Your role | Owner |

```text
GitLab Learning
```

---

## 25. Practical 5 — Create Subgroup

A subgroup was created inside the practice group:

| Setting | Value |
|---|---|
| Subgroup name | `DevOps` |
| Visibility | Private |

```text
GitLab Learning
└── DevOps
```

---

## 26. Practice Environment

The Lesson 8 practice environment was **intentionally kept separate** from the main learning project.

```text
Main project:                 Practice hierarchy:

gitlab-zero-to-production     GitLab Learning
                              └── DevOps
```

The main project was **not** moved into the practice group. This prevents accidental changes to the main learning repository while experimenting with administration concepts.

---

# 🏢 Real-World Application

## 27. Example Enterprise Structure

```text
Company
│
├── Engineering
│   ├── Backend
│   │   ├── payment-api
│   │   └── user-api
│   │
│   ├── Frontend
│   │   └── web-application
│   │
│   └── DevOps
│       ├── infrastructure
│       └── deployment
│
└── Security
    ├── Application Security
    └── Cloud Security
```

This structure helps organize ownership, teams, projects, and access.

---

## 28. Access-Control Example

| Responsibility | Example Access |
|---|---|
| External stakeholder | Guest |
| Project planner | Planner |
| Reporting / QA user | Reporter |
| Security team member | Security Manager |
| Software developer | Developer |
| Team lead / release manager | Maintainer |
| Project or group administrator | Owner |

> ⚠️ Always choose the role based on **actual responsibilities** and your GitLab instance's permission model.

---

## 29. Why Developers Should Not Automatically Have Administrative Access

A developer generally needs to:

```text
Create feature branches → Write code → Commit → Push → Create Merge Requests → Run pipelines
```

They usually do **not** need to:

- ❌ Manage project settings
- ❌ Manage all members
- ❌ Change administrative configuration

Separating these responsibilities reduces unnecessary access and risk.

---

## 30. Connection With Merge Requests

Lesson 6 introduced Merge Requests. Lesson 8 adds **access control** around that workflow:

```text
Developer
    │
    │  Developer permissions
    v
Feature Branch
    │
    v
Merge Request
    ├── Code Review
    └── CI/CD
    │
    v
Protected main
```

This is the foundation for the repository-security model covered in **Lesson 9**.

---

## 31. Connection With CI/CD

Later in the course, the broader workflow will become:

```text
Developer
     ↓
GitLab Repository
     ↓
Feature Branch
     ↓
Merge Request
     ↓
CI/CD → Testing → Security
     ↓
Approval
     ↓
Protected Main
     ↓
Deployment
```

Permissions matter because **not every user should be able to modify every part** of this workflow.

---

# 📋 Summary

## 32. Key Learnings

- [x] GitLab users
- [x] Project membership
- [x] Group membership
- [x] Groups and subgroups
- [x] Project-level vs group-level access
- [x] GitLab roles: Guest, Planner, Reporter, Security Manager, Developer, Maintainer, Owner
- [x] Temporary access
- [x] Principle of Least Privilege
- [x] Permission hierarchy and inheritance
- [x] Protected branch relationship
- [x] Group organization
- [x] Access-control planning

---

## 33. Lesson 8 Architecture

```text
                 GitLab Account
                       │
          ┌────────────┴────────────┐
          │                         │
          v                         v
 Project Membership          Group Membership
          │                         │
          v                         v
       Project                    Group
          │                         │
          │                         v
          │                     Subgroup
          │                         │
          │                         v
          │                      Project
          │                         │
          └────────────┬────────────┘
                       v
                     Role
                       │
                       v
                  Permissions
```

---

## 34. Lesson 8 Outcome

The Lesson 8 practical work established a foundation for **GitLab access management**:

```text
Users
  ↓
Groups
  ↓
Subgroups
  ↓
Projects
  ↓
Roles
  ↓
Permissions
```

These concepts will be used throughout the remaining GitLab administration, security, and CI/CD lessons.

---

### ✅ Status: Lesson 8 — Completed
