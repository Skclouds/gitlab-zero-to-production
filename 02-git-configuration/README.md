# Lesson 2 - Git Professional Setup and Configuration

## Objective

The objective of this lesson is to understand how Git can be configured for professional development environments.

Topics covered:

- git config
- Global and local Git configuration
- Git username
- Git email
- Default branch
- Git editor
- Line endings
- core.autocrlf
- Why Git configuration matters in real-world projects

---

## 1. What is git config?

git config is used to view and configure Git settings.

Example:

git config --global user.name "Your Name"

Git configuration controls how Git behaves on the developer's machine and inside individual repositories.

---

## 2. Git Configuration Levels

Git configuration can exist at different levels:

System
  |
Global
  |
Local

### System

System-level configuration applies to all users on the machine.

Command:

git config --system

### Global

Global configuration applies to the current user's Git environment across repositories.

Command:

git config --global

Example:

git config --global user.name "Your Name"

### Local

Local configuration applies only to the current repository.

Command:

git config --local

Example:

git config --local user.name "Project User"

---

## 3. Git Username

Git records author information with commits.

Example:

git config --global user.name "Your Name"

The configured name can appear in commit history.

This is useful for:

- Identifying contributors
- Code review
- Auditing
- Troubleshooting
- Team collaboration

### Current Configuration

Username: Skclouds

---

## 4. Git Email

Git also records an email address with commits.

Example:

git config --global user.email "your-email@example.com"

The email should be an address the developer wants associated with Git commits.

### Current Configuration

Email: kaushalsingh1715@gmail.com

---

## 5. Global vs Local Configuration

Global configuration applies by default across repositories.

Example:

git config --global user.name

Local configuration applies only to the current repository.

Example:

git config --local user.name

A local repository configuration can override the corresponding global configuration.

---

## 6. Default Branch

Git repositories can use a default initial branch.

A common modern convention is:

main

The default initial branch was configured using:

git config --global init.defaultBranch main

Verification:

git config --global init.defaultBranch

Output:

main

Newly initialized repositories will therefore use main as their initial branch.

---

## 7. Git Editor

Git may open a text editor when a command requires the user to enter text interactively.

Common editors include:

- Notepad
- Visual Studio Code
- Vim
- Nano

Visual Studio Code was selected as the Git editor.

Configuration:

git config --global core.editor "code --wait"

VS Code version verified during the practical:

1.134.0

The --wait option tells Git to wait until the editing operation is completed before continuing.

---

## 8. Line Endings

Different operating systems commonly use different line-ending conventions.

Windows commonly uses:

CRLF

Linux and macOS commonly use:

LF

When developers work on the same project across different operating systems, inconsistent line endings can create unnecessary Git differences.

---

## 9. core.autocrlf

Git provides the core.autocrlf configuration setting to help manage line-ending conversion.

The setting was configured using:

git config --global core.autocrlf true

Verification:

git config --global core.autocrlf

Output:

true

This setting helps Git manage line-ending conversion when working on Windows.

Professional cross-platform projects may also use a .gitattributes file to explicitly define line-ending behavior.

---

## 10. Why Git Configuration Matters

A consistent Git configuration helps provide predictable behavior across projects.

Important areas include:

Identity
  |
Branch conventions
  |
Editor behavior
  |
Line-ending handling
  |
Consistent Git workflow

Proper configuration is especially important when working with:

- Multiple developers
- Windows and Linux environments
- GitHub
- GitLab
- CI/CD pipelines
- Enterprise repositories

---

## 11. Hands-on Commands Performed

Check Git username:

git config --global user.name

Check Git email:

git config --global user.email

Configure default branch:

git config --global init.defaultBranch main

Configure Windows line-ending behavior:

git config --global core.autocrlf true

Check VS Code:

code --version

Configure VS Code as Git editor:

git config --global core.editor "code --wait"

Verify global configuration:

git config --global --list

---

## 12. Current Configuration

The following Git settings were configured during this lesson:

user.name=Skclouds
user.email=kaushalsingh1715@gmail.com
init.defaultBranch=main
core.autocrlf=true
core.editor=code --wait

---

## 13. Lesson Status

### Concepts

- [x] Understand git config
- [x] Understand configuration levels
- [x] Understand Git username
- [x] Understand Git email
- [x] Understand global configuration
- [x] Understand local configuration
- [x] Understand default branch configuration
- [x] Understand Git editor configuration
- [x] Understand line endings
- [x] Understand core.autocrlf

### Hands-on

- [x] Inspect current Git configuration
- [x] Configure Git identity
- [x] Configure default branch
- [x] Configure Git editor
- [x] Configure line-ending behavior
- [x] Verify configuration
- [ ] Test configuration in a new repository

The final hands-on test will be completed before marking Lesson 2 completely finished.

---

## Key Takeaway

Git configuration is the foundation for a consistent development workflow.

Before working on professional GitLab projects, a developer should understand:

Git Identity
  |
Git Configuration
  |
Branch Convention
  |
Editor
  |
Line Ending Handling
  |
Professional Git Workflow

This configuration will be used throughout the remaining GitLab learning journey.
