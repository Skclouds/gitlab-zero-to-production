Lesson 9 — GitLab Protected Branches \& Repository Security



\## 1. Introduction



Protected branches are an important GitLab security feature used to protect important branches such as:



\- `main`

\- `master`

\- `develop`

\- release branches

\- production branches



The main purpose of a protected branch is to prevent unauthorized or accidental changes.



In a production environment, developers normally work on feature branches and use Merge Requests to move their changes into protected branches.



\---



\# 2. Protected Branch — Layman Explanation



Think of a GitLab repository like a company.



```text

Company

│

├── Developer Area

│     └── Feature Branches

│

└── Production Area 🔒

&#x20;     └── main



Developers can work freely in their own area.



But the production area is protected.



They cannot simply walk in and change production directly.



Instead:



Developer

&#x20;   ↓

Feature Branch

&#x20;   ↓

Commit

&#x20;   ↓

Push

&#x20;   ↓

Merge Request

&#x20;   ↓

Review

&#x20;   ↓

Approval / CI checks

&#x20;   ↓

main 🔒



This is the basic idea behind protected branches.



3\. Why Protect main?



The main branch commonly represents the stable version of the project.



Without protection, someone could accidentally execute:



git push origin main



and directly change the branch.



In a production workflow, we generally want changes to go through a controlled process.



Feature Branch

&#x20;     ↓

Merge Request

&#x20;     ↓

Code Review

&#x20;     ↓

CI/CD Checks

&#x20;     ↓

Protected main



This provides better control over changes.



4\. Normal Branch vs Protected Branch

Normal Branch



A normal branch generally allows developers to push their changes.



Example:



feature/login

feature/payment

feature/user-profile



Developer workflow:



git add .

git commit -m "Add login feature"

git push origin feature/login

Protected Branch



A protected branch has restrictions on who can push or merge.



Example:



main 🔒



The branch can be configured so that direct pushes are not allowed.



Instead, developers create a Merge Request.



5\. Production Example



Imagine a company has this repository:



ecommerce-application



The production branch is:



main



A developer wants to add a payment feature.



Instead of changing main directly:



Developer

&#x20;  │

&#x20;  ▼

feature/payment

&#x20;  │

&#x20;  ├── Code

&#x20;  ├── Commit

&#x20;  └── Push

&#x20;        │

&#x20;        ▼

&#x20;   Merge Request

&#x20;        │

&#x20;        ▼

&#x20;  Code Review

&#x20;        │

&#x20;        ▼

&#x20;     CI/CD

&#x20;        │

&#x20;        ▼

&#x20;    main 🔒



This makes the workflow controlled and auditable.



6\. GitLab Protected Branch Configuration



In the GitLab project, protected branches can be configured from:



Project

&#x20;  ↓

Settings

&#x20;  ↓

Repository

&#x20;  ↓

Protected branches



GitLab also provides Branch rules, which brings branch protection, approval rules and status checks together.



7\. Our main Branch Configuration



For this learning project, the main branch was configured as a protected branch.



The configuration is:



Setting	Value

Branch	main

Allowed to merge	Maintainers

Allowed to push and merge	No one

Allowed to force push	OFF



Therefore:



main 🔒



Merge → Maintainers

Direct Push → No one

Force Push → Disabled

8\. Meaning of Each Setting

Allowed to merge



This controls who can merge Merge Requests into the protected branch.



Our configuration:



Allowed to merge → Maintainers



Therefore, the merge process is restricted to the configured role.



Allowed to push and merge



This controls who can directly push changes to the protected branch.



Our configuration:



Allowed to push and merge → No one



This is important because it encourages the Merge Request workflow.



Developer

&#x20;   ↓

Feature Branch

&#x20;   ↓

Merge Request

&#x20;   ↓

Maintainer

&#x20;   ↓

main

Allowed to force push



Force pushing can rewrite branch history.



For example:



git push --force



Force pushing to an important branch can be dangerous because commits can be rewritten or removed from the branch history.



Our configuration:



Allowed to force push → OFF

9\. Lesson 8 Connection — Roles and Protected Branches



Protected branches are closely related to GitLab roles.



In Lesson 8, we learned that different GitLab roles have different levels of access.



For example:



Guest

Planner

Reporter

Developer

Maintainer

Owner



A Developer can normally work with feature branches and create Merge Requests.



A protected branch can then restrict who is allowed to merge or push to important branches.



Therefore:



GitLab Role

&#x20;    +

Branch Protection

&#x20;    ↓

Access Control



This is an important DevOps security concept.



10\. Hands-on Practice

Step 1 — Check the repository



Our GitLab repository:



gitlab-zero-to-production



Local repository:



C:\\Users\\ASPL-PUNE\\gitlab-zero-to-production-gitlab



Check the repository:



git status



Check the current branch:



git branch --show-current



Check recent commits:



git log --oneline -3

11\. Create a Test Branch



A temporary branch was created:



git switch -c test/protected-main



The current branch became:



test/protected-main

12\. Create a Test File



A test file was created:



echo Protected branch test - Lesson 9 > lesson9-protection-test.txt



Check the working tree:



git status



The file initially appeared as an untracked file.



13\. Stage and Commit the File



The file was staged:



git add lesson9-protection-test.txt



Then committed:



git commit -m "Test protected main branch"



The resulting commit was:



04cfaa7 Test protected main branch

14\. Push the Test Branch



The test branch was pushed to GitLab:



git push -u origin test/protected-main



This succeeded.



Why?



Because:



test/protected-main



was not a protected branch.



Therefore, the normal branch push was allowed.



15\. Direct Push Test



The following command was used to test a direct push toward main:



git push origin HEAD:main



The push was rejected with:



! \[rejected] HEAD -> main (fetch first)



Git also reported:



remote contains work that you do not have locally

Important Learning



This particular rejection was caused by Git's branch-history/fast-forward protection.



It was not proof by itself that GitLab's protected-branch rule rejected the push.



This distinction is important.



There are two different mechanisms:



Git

&#x20;↓

Checks branch history

&#x20;↓

Fast-forward / history rules



and:



GitLab

&#x20;↓

Checks branch permissions

&#x20;↓

Protected branch rules



Therefore, a fetch first rejection should not automatically be interpreted as a protected-branch rejection.



16\. Git Protection vs GitLab Protection

Git-level protection



Git may reject a push when the remote branch contains commits that the local branch does not have.



Example:



! \[rejected] HEAD -> main (fetch first)



This is related to branch history.



GitLab protected branch



GitLab can reject a push because the target branch is protected and the user is not allowed to push to it.



Conceptually:



Local Git

&#x20;  ↓

Push

&#x20;  ↓

GitLab

&#x20;  ↓

Is branch protected?

&#x20;  ↓

Is user allowed to push?

&#x20;  ↓

NO

&#x20;  ↓

Reject



These are separate checks.



17\. Safe Production Workflow



A recommended protected-branch workflow can look like:



&#x20;                 GitLab Repository

&#x20;                        │

&#x20;                        ▼

&#x20;                   main 🔒

&#x20;                        ▲

&#x20;                        │

&#x20;                  Merge Request

&#x20;                        ▲

&#x20;                        │

&#x20;                 feature branch

&#x20;                        ▲

&#x20;                        │

&#x20;                    Developer



Detailed workflow:



1\. Create feature branch

&#x20;       ↓

2\. Develop code

&#x20;       ↓

3\. Commit changes

&#x20;       ↓

4\. Push feature branch

&#x20;       ↓

5\. Create Merge Request

&#x20;       ↓

6\. Review changes

&#x20;       ↓

7\. Run CI/CD checks

&#x20;       ↓

8\. Maintainer merges

&#x20;       ↓

9\. main 🔒

18\. Why This Matters in DevOps



Protected branches become especially important when GitLab is connected with CI/CD.



For example:



Developer

&#x20;   ↓

GitLab Feature Branch

&#x20;   ↓

Merge Request

&#x20;   ↓

Jenkins / GitLab CI

&#x20;   ↓

Unit Tests

&#x20;   ↓

SonarQube

&#x20;   ↓

Security Checks

&#x20;   ↓

Approval

&#x20;   ↓

Protected main

&#x20;   ↓

Build Artifact

&#x20;   ↓

JFrog Artifactory

&#x20;   ↓

Deployment



This prevents an uncontrolled change from easily reaching the production workflow.



19\. Security Principles Learned

Principle 1 — Protect important branches

main 🔒

production 🔒

release/\* 🔒

Principle 2 — Avoid unnecessary direct pushes



Use:



Feature Branch

&#x20;    ↓

Merge Request

&#x20;    ↓

Review

&#x20;    ↓

Merge

Principle 3 — Disable unnecessary force pushes



Force pushes can rewrite branch history.



For important branches:



Force Push → OFF

Principle 4 — Use role-based access



Give users only the permissions they need.



This follows the principle of:



Least Privilege



20\. Important Commands

Check current branch

git branch --show-current

Check status

git status

Create a branch

git switch -c feature/my-feature

Push a branch

git push -u origin feature/my-feature

Fetch remote information

git fetch origin

View recent commits

git log --oneline -3

Attempt a direct push

git push origin HEAD:main

21\. Key Takeaways



After completing Lesson 9, I understand:



What a protected branch is.

Why main should be protected.

The difference between a normal branch and a protected branch.

How protected branches support the Merge Request workflow.

The meaning of "Allowed to merge".

The meaning of "Allowed to push and merge".

Why force push should normally be disabled on important branches.

How GitLab roles and branch protection work together.

How to configure protection for main.

The difference between Git's history/fast-forward rejection and GitLab's protected-branch permission rejection.

Why protected branches are important in production DevOps workflows.

22\. Lesson 9 Completion

Lesson 9 — GitLab Protected Branches \& Repository Security



Theory              ✅

Layman explanation   ✅

Production example  ✅

main protection     ✅

Test branch         ✅

Commit practice     ✅

Push practice       ✅

Security concepts   ✅



Status: Lesson 9 Complete

