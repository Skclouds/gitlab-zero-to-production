# Lesson 4 — GitLab Project & Repository Management

## Objective

The objective of this lesson is to understand how Git repositories are managed locally and remotely using GitLab.

### Topics Covered

- GitLab Projects and Repositories
- Local and Remote Repositories
- Cloning a GitLab Repository
- Git Remotes
- The `origin` Remote
- Git Push and Pull Operations
- Git Fetch vs Git Pull
- Local and Remote Branches
- Repository Files
- `.gitignore`
- `.gitattributes`
- Git Tags
- GitLab Releases
- Local-to-Remote Git Workflow
- Real-World DevOps Repository Workflow

---

## 1. GitLab Project vs Repository

A **GitLab Project** is the complete workspace for a software project.

A GitLab Project can contain:

- Git Repository
- Issues
- Merge Requests
- CI/CD Pipelines
- Wiki
- Project Members
- Package Registry
- Container Registry
- Security Features
- Project Settings

The **Git Repository** is the part of the GitLab Project that stores the project's source code and version history.

A repository contains:

- Source Code
- Files
- Commits
- Branches
- Tags
- Version History

### Simple Example

Think of a **GitLab Project as a company office**.

The **Git Repository is the file/document storage inside that office**.

The project contains the repository along with collaboration, CI/CD, security, and project-management features.

---

## 2. Local Repository vs Remote Repository

A Git repository can exist in two important locations:

1. Local Repository
2. Remote Repository

### 2.1 Local Repository

A **local repository** is the Git repository stored on the developer's computer.

It contains the project's Git history and allows developers to work on the project locally.

Example:

```text
C:\Users\ASPL-PUNE\gitlab-zero-to-production
```

Developers can create commits, create branches, inspect history, and perform other Git operations locally.

### 2.2 Remote Repository

A **remote repository** is a repository hosted on a remote Git server such as GitLab.

Example:

```text
git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git
```

The remote repository acts as a shared repository for the development team.

---

## 3. Git Repository Workflow

The basic Git workflow can be represented as:

```text
Working Directory
       |
       | git add
       v
Staging Area
       |
       | git commit
       v
Local Repository
       |
       | git push
       v
Remote Repository
   (GitLab)
```

Changes from GitLab can also be retrieved into the local repository using:

```bash
git fetch
```

or:

```bash
git pull
```

---

## 4. Cloning a GitLab Repository

Cloning creates a local copy of a remote GitLab repository.

```bash
git clone git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git
```

A cloned repository normally contains:

- Project files
- Commit history
- Branch information
- Git metadata
- Remote configuration

The clone allows developers to work on the project locally.

### Example

Clone the repository:

```bash
git clone git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git
```

Navigate into the repository:

```bash
cd gitlab-zero-to-production
```

Check the repository status:

```bash
git status
```

---

## 5. Git Remote

A **Git remote** is a reference to another Git repository.

In this learning project, the remote points to the GitLab repository.

To view the configured remotes:

```bash
git remote -v
```

Example output:

```text
origin  git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git (fetch)
origin  git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git (push)
```

The output shows the remote repository URL used for:

- Fetching changes
- Pushing changes

---

## 6. What Is `origin`?

`origin` is the conventional default name Git assigns to the remote repository when a repository is cloned.

> **Important:** `origin` ≠ GitLab
>
> `origin` is simply a nickname/reference for a remote repository.

```text
origin
   |
   v
GitLab Repository
```

A remote repository can have another name as well:

```bash
git remote add upstream <repository-url>
```

In this case, `upstream` becomes another remote name.

---

## 7. Git Push

The `git push` command sends local commits to a remote repository.

```bash
git push origin main
```

The command can be understood as:

```text
git push
     |
     +-- origin = remote repository
     |
     +-- main   = branch
```

### Workflow

```text
Local Commit
     |
     | git push
     v
GitLab Repository
```

### Example

```bash
git add .
git commit -m "Add repository management practice"
git push origin main
```

After a successful push, the commit becomes available in the remote GitLab repository.

---

## 8. Git Pull

The `git pull` command retrieves changes from the remote repository and integrates them into the current local branch.

```bash
git pull origin main
```

Conceptually:

```text
GitLab
   |
   | Fetch Changes
   v
Local Repository
   |
   | Integrate Changes
   v
Current Branch
```

`git pull` is commonly used when other developers have pushed changes to the remote repository and you want to bring those changes into your current branch.

---

## 9. Git Fetch

The `git fetch` command downloads changes from the remote repository without automatically integrating them into the current working branch.

```bash
git fetch origin
```

Conceptually:

```text
GitLab
   |
   | git fetch
   v
Local Remote-Tracking Information
```

The current working branch is not automatically changed by `git fetch`.

This makes fetch useful when you want to inspect remote changes before deciding how to integrate them.

---

## 10. Git Fetch vs Git Pull

| Command     | Purpose                                                                    |
|-------------|----------------------------------------------------------------------------|
| `git fetch` | Downloads remote changes without integrating them into the current branch |
| `git pull`  | Fetches remote changes and integrates them into the current branch        |

### Simple Way to Remember

```text
fetch = "bring information"
pull  = "bring + integrate"
```

---

## 11. Checking Local and Remote Branches

Git provides commands to inspect local and remote branches.

**View local branches:**

```bash
git branch
```

**View remote branches:**

```bash
git branch -r
```

**View both local and remote branches:**

```bash
git branch -a
```

This is useful for understanding the relationship between local branches and branches available on the remote repository.

---

## 12. Changing a Remote URL

A configured remote URL can be changed using:

```bash
git remote set-url origin <new-url>
```

Example:

```bash
git remote set-url origin git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git
```

Verify the updated remote:

```bash
git remote -v
```

---

## 13. `README.md`

`README.md` is commonly used to document a project.

A README can contain:

- Project Overview
- Installation Instructions
- Usage Instructions
- Technologies Used
- Architecture
- Configuration
- CI/CD Information
- Contribution Instructions

GitLab can display the README directly on the project repository page.

---

## 14. `.gitignore`

The `.gitignore` file tells Git which files and directories should normally not be tracked.

Example:

```gitignore
node_modules/
.env
*.log
target/
__pycache__/
```

Common examples of files that should not normally be committed include:

- Environment files
- Secrets
- Password files
- Temporary files
- Build output
- Dependency directories
- Log files

### Example

An `.env` file may contain sensitive configuration such as:

```env
DATABASE_PASSWORD=******
API_KEY=******
```

> **Note:** Sensitive information such as passwords, API keys, and credentials should not be committed to a Git repository.

---

## 15. `.gitattributes`

The `.gitattributes` file is used to define attributes and handling rules for files and paths within a Git repository.

Example:

```gitattributes
*.sh  text eol=lf
*.bat text eol=crlf
*.png binary
```

It helps Git determine how particular files should be handled (for example, line endings and binary detection).

### `.gitignore` vs `.gitattributes`

```text
.gitignore
    |
    +-- Controls which files Git should normally ignore

.gitattributes
    |
    +-- Controls attributes and handling of files
```

### Key Difference

| File             | Purpose                                                    |
|------------------|------------------------------------------------------------|
| `.gitignore`     | Defines files and directories that Git should normally ignore |
| `.gitattributes` | Defines attributes and handling rules for files and paths  |

---

## 16. Git Tags

A **Git tag** is a reference that marks an important point in a repository's history.

Tags are commonly used to identify software versions, for example:

```text
v1.0.0
v1.1.0
v2.0.0
```

### 16.1 Create a Lightweight Tag

```bash
git tag v1.0.0
```

### 16.2 Create an Annotated Tag

```bash
git tag -a v1.0.0 -m "Release version 1.0.0"
```

Annotated tags can contain additional information such as a message and tag metadata (author, date).

### 16.3 List Tags

```bash
git tag
```

### 16.4 Push a Tag

```bash
git push origin v1.0.0
```

### 16.5 Push All Tags

```bash
git push origin --tags
```

---

## 17. GitLab Releases

A **GitLab Release** represents a specific version of a software project.

Releases are commonly associated with Git tags.

```text
Git Commit
     |
     v
Git Tag (v1.0.0)
     |
     v
GitLab Release (Version 1.0.0)
```

A release can be used to communicate a specific software version and its associated changes.

For example:

```text
Version: v1.0.0

Release:
- Initial production version
- Added authentication
- Added database integration
- Fixed application startup issue
```

---

## 18. Important Git Commands

| Command         | Purpose                                            |
|-----------------|----------------------------------------------------|
| `git clone`     | Creates a local copy of a remote repository        |
| `git status`    | Shows the current repository state                 |
| `git remote -v` | Displays configured remote URLs                    |
| `git branch`    | Displays local branches                            |
| `git branch -r` | Displays remote branches                           |
| `git add`       | Stages changes                                     |
| `git commit`    | Creates a local commit                             |
| `git push`      | Sends commits to a remote repository               |
| `git fetch`     | Downloads remote changes without integrating them  |
| `git pull`      | Fetches and integrates remote changes              |
| `git log`       | Displays commit history                            |
| `git tag`       | Creates or lists Git tags                          |

---

## 19. Real-World DevOps Example

Consider a development team working on an application.

```text
Developer A
     |
     | git push
     v
GitLab Repository
     ^
     |
     | git pull
     |
Developer B
```

### Developer A

Developer A develops a new feature and pushes the changes:

```bash
git add .
git commit -m "Add new feature"
git push origin main
```

The changes are now available in the GitLab repository.

### Developer B

Developer B can retrieve the latest changes using:

```bash
git pull origin main
```

For safer inspection of remote changes, Developer B can first use:

```bash
git fetch origin
```

The remote changes can then be inspected before deciding how to integrate them.

### DevOps Perspective

In a production DevOps environment, the GitLab repository acts as the central source of truth for application source code and the starting point for CI/CD automation.

A typical workflow looks like:

```text
Developer
    |
    v
GitLab Repository
    |
    v
CI/CD Pipeline
    |
    +---- Build
    |
    +---- Test
    |
    +---- Code Quality
    |
    +---- Security Checks
    |
    v
Artifact / Container
    |
    v
Deployment
```

---

## 20. Key Takeaways

After completing this lesson, the following concepts should be understood:

- A GitLab Project can contain a Git repository and other DevOps features.
- A repository stores source code and version history.
- A local repository exists on the developer's machine.
- A remote repository can be hosted on GitLab.
- `origin` is the conventional name for a remote repository.
- `git clone` creates a local copy of a remote repository.
- `git push` sends local commits to a remote repository.
- `git pull` retrieves and integrates remote changes.
- `git fetch` retrieves remote information without automatically integrating it into the current branch.
- `.gitignore` defines files and directories that Git should normally ignore.
- `.gitattributes` defines attributes and handling rules for files.
- Git tags identify important points in repository history.
- GitLab Releases can represent formal software versions.
- Git repositories provide the foundation for collaborative development and CI/CD workflows.

---

## 21. Lesson 4 Status

### Concepts Completed

- [x] GitLab Project vs Repository
- [x] Local vs Remote Repository
- [x] Git Clone
- [x] Git Remote
- [x] `origin`
- [x] Git Push
- [x] Git Pull
- [x] Git Fetch
- [x] Local and Remote Branches
- [x] `README.md`
- [x] `.gitignore`
- [x] `.gitattributes`
- [x] Git Tags
- [x] GitLab Releases
- [x] Repository Workflow
- [x] Real-World DevOps Example
