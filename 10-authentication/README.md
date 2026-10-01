# Lesson 10 — GitLab Authentication

## 🎯 Objective

The objective of this lesson was to understand **authentication** in GitLab — how users and automation tools prove their identity — and to configure **SSH key authentication** for the learning repository, replacing HTTPS.

---

## 📚 Table of Contents

**Concepts**

1. [Introduction](#1-introduction)
2. [Authentication vs Authorization](#2-authentication-vs-authorization)
3. [GitLab Authentication Methods](#3-gitlab-authentication-methods)
4. [HTTPS Authentication](#4-https-authentication)
5. [Why Avoid Using a Personal Password for Automation?](#5-why-avoid-using-a-personal-password-for-automation)
6. [Personal Access Token](#6-personal-access-token)
7. [Token Scopes](#7-token-scopes)
8. [Other Token Types](#8-other-token-types)
9. [Token Security](#9-token-security)
10. [SSH Authentication](#10-ssh-authentication)
11. [Public Key vs Private Key](#11-public-key-vs-private-key)

**Hands-On**

12. [Initial SSH Situation](#12-initial-ssh-situation)
13. [Generate an SSH Key](#13-generate-an-ssh-key)
14. [Add Public Key to GitLab](#14-add-public-key-to-gitlab)
15. [Test SSH Authentication](#15-test-ssh-authentication)
16. [HTTPS Repository Configuration](#16-https-repository-configuration)
17. [Change GitLab Remote from HTTPS to SSH](#17-change-gitlab-remote-from-https-to-ssh)
18. [Test Git Operations Through SSH](#18-test-git-operations-through-ssh)
19. [Final Authentication Flow](#19-final-authentication-flow)

**Comparison & DevOps**

20. [HTTPS vs SSH](#20-https-vs-ssh)
21. [Token vs SSH](#21-token-vs-ssh)
22. [Authentication in DevOps](#22-authentication-in-devops)
23. [Authentication and Security Principles](#23-authentication-and-security-principles)

**Summary**

24. [Important Commands](#24-important-commands)
25. [Lesson 10 Practical Summary](#25-lesson-10-practical-summary)
26. [Key Takeaways](#26-key-takeaways)
27. [Lesson 10 Completion](#27-lesson-10-completion)

---

## 1. Introduction

**Authentication** is the process of proving the identity of a user, system, or application.

In GitLab and DevOps, authentication is required whenever a user or automation tool needs to access GitLab resources.

Common authentication mechanisms include:

- Username/password authentication
- Two-Factor Authentication (2FA)
- SSH keys
- Personal Access Tokens
- Project Access Tokens
- Group Access Tokens
- Deploy Tokens
- OAuth and external identity providers

---

## 2. Authentication vs Authorization

These are **different concepts**.

| | Authentication | Authorization |
|---|---|---|
| **Question** | **Who are you?** | **What are you allowed to do?** |
| **GitLab example** | Password, SSH key, or token verifies your identity | Your role (Developer, Maintainer…) decides your actions |
| **Covered in** | This lesson | Lessons 8 & 9 |

Authentication:

```text
User
 ↓
Provides credential
 ↓
GitLab verifies identity
 ↓
Identity confirmed
```

Authorization:

```text
User
 ↓
Authenticated
 ↓
GitLab checks role/permissions
 ↓
Allowed actions determined
```

### 🏢 Real-life example

An employee enters a company:

- Showing an **ID card** at the gate proves who they are → **Authentication**
- Being allowed into the development room but **not** the server room → **Authorization**

```text
Authentication
       +
Authorization
       ↓
Secure Access
```

---

## 3. GitLab Authentication Methods

```text
GitLab Authentication
│
├── Password
├── Two-Factor Authentication
├── SSH Keys
├── Personal Access Tokens
├── Project Access Tokens
├── Group Access Tokens
├── Deploy Tokens
└── OAuth / External Identity Providers
```

Different mechanisms are useful for different scenarios.

---

## 4. HTTPS Authentication

Git repositories can communicate with GitLab over HTTPS:

```text
https://gitlab.com/kaushalsingh1715/gitlab-zero-to-production.git
```

```text
Developer
   │
   │ HTTPS
   ▼
GitLab
   │
   ▼
Credential
   │
   ▼
Authentication
```

> 💡 For Git operations over HTTPS, an **access token** is used instead of your GitLab account password. (If 2FA is enabled, a password won't work for Git over HTTPS at all.)

---

## 5. Why Avoid Using a Personal Password for Automation?

Consider a Jenkins server that needs to access a GitLab repository.

❌ **Poor approach:**

```text
Jenkins
   ↓
User's personal GitLab password
   ↓
GitLab
```

If Jenkins is compromised, the attacker gets **full access to your entire account**.

✅ **Better approach:**

```text
Jenkins
   ↓
Controlled credential/token (limited scope)
   ↓
GitLab
```

This follows the **Principle of Least Privilege** — grant only the permissions required for the task.

---

## 6. Personal Access Token

A **Personal Access Token (PAT)** is a credential tied to a GitLab user account. It can be used for authenticated access to GitLab resources and APIs, depending on its scopes.

```text
GitLab Account
      │
      └── Personal Access Token
              │
              ├── Scope
              └── Expiration
```

> ⚠️ A token should be treated **like a password** and must never be exposed publicly.

---

## 7. Token Scopes

A token's **scope** determines what it can access.

| Scope | Access | Typical use |
|---|---|---|
| `read_repository` | Read repository | Clone, pull, fetch |
| `write_repository` | Read + write repository | Clone, pull, fetch, **push** |
| `api` | Broad API access | Full API automation |

> ⚠️ `api` is very broad — don't select it when a more limited scope is sufficient.

```text
Required permission
       ↓
Choose smallest suitable scope
       ↓
Least privilege
```

---

## 8. Other Token Types

| Credential | Tied to | Best for |
|---|---|---|
| **Personal Access Token** | A user account | Personal scripts, API access as yourself |
| **Project Access Token** | A single project (bot user) | Automation for one project |
| **Group Access Token** | A group (bot user) | Automation across all projects in a group |
| **Deploy Token** | A project or group | Read-only clone / registry pull from servers |
| **Deploy Key** | A project (SSH key) | Server needing SSH access to one repository |

> 💡 For automation (Jenkins, servers, CI), prefer **project/group tokens or deploy tokens** over a Personal Access Token, so the credential isn't tied to a human.

---

## 9. Token Security

Important security practices:

- ❌ Never commit tokens into Git repositories
- ❌ Never put tokens directly in source code
- ❌ Never share tokens in chat
- ❌ Never publish tokens in screenshots
- ✅ Use the smallest required scope
- ✅ Set an expiration date
- ✅ Rotate/revoke credentials when necessary
- ✅ Store credentials in a secure credential manager

**What not to do:**

```python
GITLAB_TOKEN = "my-secret-token"   # ❌ hard-coded secret
```

Instead, store credentials securely and **inject them** into the application or pipeline when required (e.g. GitLab CI/CD variables — covered in a later lesson).

> 🚨 If a token is ever leaked, **revoke it immediately** in GitLab — deleting it from the code is not enough, because it remains in Git history.

---

## 10. SSH Authentication

SSH is another common way to authenticate Git operations. It uses a **key pair**:

```text
Private Key 🔐   → stays on your computer
Public Key       → added to GitLab
```

```text
Your Computer
│
├── Private Key 🔐
│
└── Public Key
        │
        ▼
      GitLab
```

GitLab verifies that the private key being used **matches** the public key registered on your account.

> 🔑 **Analogy:** The public key is like a **padlock** you give to GitLab. Only your private key can open it. You can hand out padlocks freely — but never the key.

---

## 11. Public Key vs Private Key

| File | Type | What to do with it |
|---|---|---|
| `id_ed25519` | 🔐 **Private key** | **NEVER share.** Keep it only on your computer |
| `id_ed25519.pub` | Public key | Add it to GitLab |

```text
id_ed25519      → PRIVATE KEY → NEVER SHARE
id_ed25519.pub  → PUBLIC KEY  → Add to GitLab
```

---

# 🛠️ Hands-On

## 12. Initial SSH Situation

Before configuring SSH, the `.ssh` directory contained only:

```text
known_hosts
known_hosts.old
```

There was **no** key pair (`id_ed25519` / `id_ed25519.pub`), so an SSH connection to GitLab failed:

```text
git@gitlab.com: Permission denied (publickey).
```

This meant GitLab did not recognize any valid SSH key for the account.

---

## 13. Generate an SSH Key

An **ED25519** SSH key was generated:

```bash
ssh-keygen -t ed25519 -C "kaushalsingh1715@gmail.com"
```

This created:

```text
id_ed25519       (private key)
id_ed25519.pub   (public key)
```

✅ The private key was protected with a **passphrase**.

> 💡 ED25519 is the modern, recommended key type — shorter, faster, and more secure than older RSA keys.

---

## 14. Add Public Key to GitLab

The public key was displayed:

```bash
type %USERPROFILE%\.ssh\id_ed25519.pub     # Windows CMD
cat ~/.ssh/id_ed25519.pub                  # Git Bash / macOS / Linux
```

The output was copied and added in GitLab:

```text
Avatar → Edit profile → SSH Keys → Add new key
```

✅ Only the **public** key was added. The private key was never shared.

---

## 15. Test SSH Authentication

```bash
ssh -T git@gitlab.com
```

GitLab responded:

```text
Welcome to GitLab, @kaushalsingh1715!
```

> ℹ️ On the very first connection, SSH asks whether to trust GitLab's host fingerprint. Typing `yes` saves it to `known_hosts`.

This confirmed:

```text
Computer
   ↓
SSH Private Key
   ↓
GitLab
   ↓
Public Key Match
   ↓
Authentication Successful ✅
```

---

## 16. HTTPS Repository Configuration

Initially, the repository used HTTPS:

```bash
git remote -v
```

```text
origin  https://gitlab.com/kaushalsingh1715/gitlab-zero-to-production.git (fetch)
origin  https://gitlab.com/kaushalsingh1715/gitlab-zero-to-production.git (push)
```

---

## 17. Change GitLab Remote from HTTPS to SSH

```bash
git remote set-url origin git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git
git remote -v
```

Final configuration:

```text
origin  git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git (fetch)
origin  git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git (push)
```

---

## 18. Test Git Operations Through SSH

```bash
git fetch origin
```

```text
From gitlab.com:kaushalsingh1715/gitlab-zero-to-production
   92c5537..25506ec  main -> origin/main
```

✅ The repository successfully communicated with GitLab over SSH.

> 💡 `92c5537..25506ec` means `origin/main` moved forward from the Lesson 6 merge commit to a newer commit (e.g. the Lesson 7 merge). Run `git pull` on `main` to bring your local branch up to date.

---

## 19. Final Authentication Flow

```text
Windows PC
    │
    │ SSH
    ▼
Private Key 🔐
    │
    ▼
GitLab
    │
    │ Public Key Verification
    ▼
Authentication
    │
    ▼
Git Repository
    │
    ├── fetch
    ├── pull
    └── push
```

---

# ⚖️ Comparison & DevOps

## 20. HTTPS vs SSH

| Feature | HTTPS | SSH |
|---|---|---|
| Authentication | Token / credential | SSH key |
| Repository URL | `https://...` | `git@...` |
| Secret to protect | Access token | Private key |
| Suitable for developers | ✅ | ✅ |
| Suitable for automation | ✅ | ✅ |
| Repeated credential prompts | Depends on credential manager | No (passphrase can be cached by `ssh-agent`) |
| Works through strict firewalls | Usually (port 443) | Sometimes blocked (port 22) |

Neither is universally "better" — the right choice depends on the user, application, security requirements, and environment.

---

## 21. Token vs SSH

| | SSH Key | Access Token |
|---|---|---|
| Flow | Computer → Private SSH key → GitLab | Application/User → Token → GitLab |
| Used for | Git operations (clone, pull, push) | Git over HTTPS **and** the GitLab API |
| Best for | Developer workstations | API calls, automation, scripts |

---

## 22. Authentication in DevOps

Authentication is used throughout a DevOps toolchain:

```text
Developer
    ↓
GitLab
    ↓
Jenkins
    ↓
SonarQube
    ↓
JFrog Artifactory
    ↓
Docker Registry
    ↓
Kubernetes
```

Each system may require its own credentials:

```text
Jenkins       → GitLab credential   → GitLab
GitLab CI/CD  → Registry credential → Container Registry
```

Credentials should be managed separately and **never hard-coded** into source code.

---

## 23. Authentication and Security Principles

### Principle 1 — Never expose secrets

Never commit passwords, tokens, private SSH keys, or API keys into Git repositories.

### Principle 2 — Use least privilege

```text
Need read access?   → Read permission only
Need write access?  → Write permission
```

### Principle 3 — Use expiration and rotation

Review and rotate credentials according to organizational security requirements.

### Principle 4 — Separate human and automation credentials

For production systems, avoid personal account credentials when a dedicated **project, group, or deploy** credential is appropriate.

### Principle 5 — Enable Two-Factor Authentication

Protect your GitLab account itself with 2FA:

```text
Avatar → Edit profile → Account → Enable two-factor authentication
```

---

# 📋 Summary

## 24. Important Commands

| Command | Purpose |
|---|---|
| `git remote -v` | Check Git remote URLs |
| `git --version` | Check Git version |
| `ssh-keygen -t ed25519 -C "email@example.com"` | Generate an ED25519 SSH key |
| `ssh -T git@gitlab.com` | Test GitLab SSH authentication |
| `git remote set-url origin git@gitlab.com:USERNAME/PROJECT.git` | Change remote to SSH |
| `git fetch origin` | Fetch from GitLab |
| `git config --global --list` | Check global Git configuration |
| `dir %USERPROFILE%\.ssh` (CMD) / `ls ~/.ssh` (Bash) | List SSH directory |
| `type %USERPROFILE%\.ssh\id_ed25519.pub` (CMD) / `cat ~/.ssh/id_ed25519.pub` (Bash) | Show public key |

---

## 25. Lesson 10 Practical Summary

| Activity | Status |
|---|---|
| Check GitLab repository remote | ✅ |
| Check Git configuration | ✅ |
| Check SSH directory | ✅ |
| Generate ED25519 SSH key | ✅ |
| Protect SSH key with passphrase | ✅ |
| Add public key to GitLab | ✅ |
| Test SSH authentication | ✅ |
| Change Git remote HTTPS → SSH | ✅ |
| Test Git fetch over SSH | ✅ |

Successful SSH authentication:

```text
Welcome to GitLab, @kaushalsingh1715!
```

---

## 26. Key Takeaways

After completing Lesson 10, I understand:

- [x] What authentication means
- [x] The difference between authentication and authorization
- [x] Why authentication is important in GitLab
- [x] How HTTPS authentication works
- [x] What Personal Access Tokens are
- [x] What token scopes mean
- [x] The difference between `read_repository` and `write_repository`
- [x] Why broad scopes such as `api` should not be granted unnecessarily
- [x] The other token types (project, group, deploy)
- [x] What SSH authentication is
- [x] The difference between public and private SSH keys
- [x] Why private SSH keys must remain secret
- [x] How to generate an ED25519 SSH key
- [x] How to add an SSH public key to GitLab
- [x] How to test GitLab SSH authentication
- [x] How to change a Git remote from HTTPS to SSH
- [x] How to verify Git operations using SSH
- [x] Why authentication matters in Jenkins and DevOps automation
- [x] The importance of least privilege and credential security

---

## 27. Lesson 10 Completion

| Item | Status |
|---|---|
| Theory | ✅ |
| Authentication vs Authorization | ✅ |
| HTTPS Authentication | ✅ |
| SSH Authentication | ✅ |
| SSH Key Generation | ✅ |
| GitLab SSH Configuration | ✅ |
| SSH Authentication Test | ✅ |
| HTTPS → SSH Remote Change | ✅ |
| Git Fetch over SSH | ✅ |
| Security Principles | ✅ |

---

### ✅ Status: Lesson 10 — Completed
