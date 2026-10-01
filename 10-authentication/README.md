&#x20;Lesson 10 — GitLab Authentication



\## 1. Introduction



Authentication is the process of proving the identity of a user, system, or application.



In GitLab and DevOps, authentication is required whenever a user or automation tool needs to access GitLab resources.



Common authentication mechanisms include:



\- Username/password authentication

\- Two-Factor Authentication (2FA)

\- SSH keys

\- Personal Access Tokens

\- Project Access Tokens

\- Group Access Tokens

\- Deploy Tokens

\- OAuth and external identity providers



\---



\# 2. Authentication vs Authorization



Authentication and authorization are different concepts.



\## Authentication



Authentication answers:



> \*\*Who are you?\*\*



Example:



```text

User

&#x20;↓

Provides credential

&#x20;↓

GitLab verifies identity

&#x20;↓

Identity confirmed

Authorization



Authorization answers:



What are you allowed to do?



Example:



User

&#x20;↓

Authenticated

&#x20;↓

GitLab checks role/permissions

&#x20;↓

Allowed actions determined

Simple real-life example



An employee enters a company.



Showing an ID card proves:



Authentication



Being allowed to enter the development room but not the server room is:



Authorization



Therefore:



Authentication

&#x20;      +

Authorization

&#x20;      ↓

Secure Access

3\. GitLab Authentication Methods



Important GitLab authentication mechanisms include:



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



Different authentication mechanisms are useful for different scenarios.



4\. HTTPS Authentication



Git repositories can communicate with GitLab over HTTPS.



Example:



https://gitlab.com/kaushalsingh1715/gitlab-zero-to-production.git



Conceptually:



Developer

&#x20;  │

&#x20;  │ HTTPS

&#x20;  ▼

GitLab

&#x20;  │

&#x20;  ▼

Credential

&#x20;  │

&#x20;  ▼

Authentication



For Git operations over HTTPS, an access token can be used instead of a GitLab account password.



5\. Why Avoid Using a Personal Password for Automation?



Consider a Jenkins server that needs to access a GitLab repository.



A poor approach would be:



Jenkins

&#x20;  ↓

User's personal GitLab password

&#x20;  ↓

GitLab



This creates unnecessary security risk.



A better approach is to use a dedicated credential with limited permissions:



Jenkins

&#x20;  ↓

Controlled credential/token

&#x20;  ↓

GitLab



This follows the principle of:



Least Privilege



Only the permissions required for the task should be granted.



6\. Personal Access Token



A Personal Access Token (PAT) is a credential associated with a GitLab user account.



It can be used for authenticated access to GitLab resources and APIs depending on its assigned scopes.



Conceptually:



GitLab Account

&#x20;     │

&#x20;     └── Personal Access Token

&#x20;             │

&#x20;             ├── Scope

&#x20;             └── Expiration



A token should be treated like a password and must never be exposed publicly.



7\. Token Scopes



A token's scope determines what the token can access.



Examples include:



read\_repository

write\_repository

api

read\_repository



Provides repository read access.



Typical use:



Clone

Pull

Fetch

write\_repository



Provides repository read and write access.



Typical use:



Clone

Pull

Fetch

Push

api



Provides broader API access.



Because this scope is broader, it should not be selected when a more limited scope is sufficient.



The general principle is:



Required permission

&#x20;      ↓

Choose smallest suitable scope

&#x20;      ↓

Least privilege

8\. Token Security



Important security practices:



Never commit tokens into Git repositories.

Never put tokens directly into source code.

Never share tokens in chat.

Never publish tokens in screenshots.

Use the smallest required scope.

Set an expiration date where appropriate.

Rotate/revoke credentials when necessary.

Store credentials in a secure credential manager.



Example of what not to do:



GITLAB\_TOKEN = "my-secret-token"



Instead, credentials should be stored securely and injected into the application or pipeline when required.



9\. SSH Authentication



SSH is another common way to authenticate Git operations.



SSH uses a key pair:



Private Key 🔐

Public Key



The private key remains on the user's computer.



The public key is added to GitLab.



Conceptually:



Your Computer

│

├── Private Key 🔐

│

└── Public Key

&#x20;       │

&#x20;       ▼

&#x20;     GitLab



GitLab verifies that the private key being used corresponds to the public key registered with the account.



10\. Public Key vs Private Key

Public Key



Example:



id\_ed25519.pub



The public key can be added to GitLab.



Private Key



Example:



id\_ed25519



The private key must remain secret.



Important rule

id\_ed25519

&#x20;   ↓

PRIVATE KEY

&#x20;   ↓

NEVER SHARE

id\_ed25519.pub

&#x20;   ↓

PUBLIC KEY

&#x20;   ↓

Add to GitLab

11\. Our Initial SSH Situation



Before configuring SSH, the .ssh directory contained:



known\_hosts

known\_hosts.old



There was no SSH key pair such as:



id\_ed25519

id\_ed25519.pub



Therefore, an SSH connection to GitLab initially failed:



Permission denied (publickey)



This indicated that GitLab did not recognize a valid SSH authentication key for the account.



12\. Generate an SSH Key



An ED25519 SSH key was generated using:



ssh-keygen -t ed25519 -C "kaushalsingh1715@gmail.com"



This generated:



id\_ed25519

id\_ed25519.pub



The user protected the private key with a passphrase.



13\. Add Public Key to GitLab



The public key was displayed using:



type %USERPROFILE%\\.ssh\\id\_ed25519.pub



Only the public key was added to GitLab.



The private key was never shared.



The key was added through the GitLab SSH key settings.



14\. Test SSH Authentication



SSH authentication was tested using:



ssh -T git@gitlab.com



GitLab successfully responded:



Welcome to GitLab, @kaushalsingh1715!



This confirmed that:



Computer

&#x20;  ↓

SSH Private Key

&#x20;  ↓

GitLab

&#x20;  ↓

Public Key Match

&#x20;  ↓

Authentication Successful

15\. HTTPS Repository Configuration



Initially, the GitLab repository used HTTPS.



The remote was:



https://gitlab.com/kaushalsingh1715/gitlab-zero-to-production.git



The remote was verified using:



git remote -v



Output:



origin  https://gitlab.com/kaushalsingh1715/gitlab-zero-to-production.git (fetch)

origin  https://gitlab.com/kaushalsingh1715/gitlab-zero-to-production.git (push)

16\. Change GitLab Remote from HTTPS to SSH



After SSH authentication was successfully configured, the repository remote was changed using:



git remote set-url origin git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git



The remote was then verified:



git remote -v



Final configuration:



origin  git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git (fetch)

origin  git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git (push)

17\. Test Git Operations Through SSH



The repository connection was tested using:



git fetch origin



Git successfully communicated with GitLab.



The remote branch information was updated:



From gitlab.com:kaushalsingh1715/gitlab-zero-to-production

&#x20;  92c5537..25506ec  main -> origin/main



This confirmed that the repository itself was successfully communicating with GitLab over SSH.



18\. Final Authentication Flow



The final SSH authentication architecture is:



Windows PC

&#x20;   │

&#x20;   │ SSH

&#x20;   ▼

Private Key 🔐

&#x20;   │

&#x20;   ▼

GitLab

&#x20;   │

&#x20;   │ Public Key Verification

&#x20;   ▼

Authentication

&#x20;   │

&#x20;   ▼

Git Repository

&#x20;   │

&#x20;   ├── fetch

&#x20;   ├── pull

&#x20;   └── push

19\. HTTPS vs SSH

Feature	HTTPS	SSH

Authentication	Token/credential	SSH key

Repository URL	https://...	git@...

Secret	Access token/credential	Private key

Suitable for developers	Yes	Yes

Suitable for automation	Yes	Yes

Password repeatedly required	Depends on credential management	No

Key security	Token must be protected	Private key must be protected



Neither mechanism is universally required for every situation. The appropriate method depends on the user, application, security requirements, and environment.



20\. Token vs SSH

SSH

Computer

&#x20;  ↓

Private SSH Key

&#x20;  ↓

GitLab



Commonly convenient for developer workstations and Git operations.



Access Token

Application / User

&#x20;      ↓

Access Token

&#x20;      ↓

GitLab



Useful for controlled authenticated access and API or automation scenarios.



21\. Authentication in DevOps



Authentication is used throughout a DevOps environment.



Example:



Developer

&#x20;   ↓

GitLab

&#x20;   ↓

Jenkins

&#x20;   ↓

SonarQube

&#x20;   ↓

JFrog Artifactory

&#x20;   ↓

Docker Registry

&#x20;   ↓

Kubernetes



Each system may require its own credentials.



For example:



Jenkins

&#x20;  ↓

GitLab Credential

&#x20;  ↓

GitLab



or:



GitLab CI/CD

&#x20;  ↓

Registry Credential

&#x20;  ↓

Container Registry



Credentials should be managed separately and should not be hard-coded into source code.



22\. Authentication and Security Principles

Principle 1 — Never expose secrets



Never commit:



Passwords

Tokens

Private SSH keys

API keys



into Git repositories.



Principle 2 — Use least privilege



Give a credential only the permissions required.



Need read access?

&#x20;      ↓

Read permission



Need write access?

&#x20;      ↓

Write permission



Avoid granting broad permissions unnecessarily.



Principle 3 — Use expiration and rotation



Credentials should be reviewed and rotated according to organizational security requirements.



Principle 4 — Separate human and automation credentials



For production systems, avoid using a personal account credential when a dedicated service/project/group credential is appropriate.



23\. Important Commands

Check Git remote

git remote -v

Check Git version

git --version

Generate ED25519 SSH key

ssh-keygen -t ed25519 -C "email@example.com"

Test GitLab SSH authentication

ssh -T git@gitlab.com

Change repository remote to SSH

git remote set-url origin git@gitlab.com:USERNAME/PROJECT.git

Fetch from GitLab

git fetch origin

Check global Git configuration

git config --global --list

Check SSH directory

dir %USERPROFILE%\\.ssh

24\. Lesson 10 Practical Summary



The following practical activities were completed:



Check GitLab repository remote          ✅

Check Git configuration                 ✅

Check SSH directory                     ✅

Generate ED25519 SSH key                ✅

Protect SSH key with passphrase         ✅

Add public key to GitLab                ✅

Test SSH authentication                 ✅

Change Git remote HTTPS → SSH           ✅

Test Git fetch over SSH                 ✅



Successful SSH authentication:



Welcome to GitLab, @kaushalsingh1715!

25\. Key Takeaways



After completing Lesson 10, I understand:



What authentication means.

Difference between authentication and authorization.

Why authentication is important in GitLab.

How HTTPS authentication works.

What Personal Access Tokens are.

What token scopes mean.

Difference between read\_repository and write\_repository.

Why broad scopes such as api should not be granted unnecessarily.

What SSH authentication is.

Difference between public and private SSH keys.

Why private SSH keys must remain secret.

How to generate an ED25519 SSH key.

How to add an SSH public key to GitLab.

How to test GitLab SSH authentication.

How to change a Git remote from HTTPS to SSH.

How to verify Git operations using SSH.

Why authentication is important in Jenkins and DevOps automation.

The importance of least privilege and credential security.

26\. Lesson 10 Completion

Lesson 10 — GitLab Authentication



Theory                         ✅

Authentication vs Authorization ✅

HTTPS Authentication            ✅

SSH Authentication              ✅

SSH Key Generation              ✅

GitLab SSH Configuration        ✅

SSH Authentication Test         ✅

HTTPS → SSH Remote Change       ✅

Git Fetch over SSH              ✅

Security Principles             ✅



Status: Lesson 10 Complete

