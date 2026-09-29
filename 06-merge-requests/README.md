\# Lesson 6 — GitLab Merge Requests



\## 🎯 Objective



The objective of this lesson was to understand \*\*GitLab Merge Requests (MRs)\*\*, their purpose in professional development workflows, and how developers use branches and Merge Requests to safely introduce changes into the `main` branch.



This lesson also included a \*\*complete hands-on Merge Request workflow\*\* using an actual GitLab repository.



\---



\## 📚 Table of Contents



\*\*Concepts\*\*



1\. \[What Is a Merge Request?](#1-what-is-a-merge-request)

2\. \[Real-Life Analogy](#2-real-life-analogy)

3\. \[Why Merge Requests Are Important](#3-why-merge-requests-are-important)

4\. \[Source Branch and Target Branch](#4-source-branch-and-target-branch)

5\. \[Merge Request vs Git Merge](#5-merge-request-vs-git-merge)

6\. \[Merge Request Title](#6-merge-request-title)

7\. \[Merge Request Description](#7-merge-request-description)

8\. \[Merge Request Changes / Diff](#8-merge-request-changes--diff)

9\. \[Code Review](#9-code-review)

10\. \[Merge Request Lifecycle](#10-merge-request-lifecycle)

11\. \[Draft Merge Requests](#11-draft-merge-requests)

12\. \[Merge Request Approvals](#12-merge-request-approvals)

13\. \[CI/CD and Merge Requests](#13-cicd-and-merge-requests)

14\. \[Merge Conflicts](#14-merge-conflicts)

15\. \[Merged vs Closed](#15-merged-vs-closed)



\*\*Hands-On\*\*



16\. \[Repository Setup](#16-repository-setup)

17\. \[Create Feature Branch](#17-create-feature-branch)

18\. \[Create Practice File](#18-create-practice-file)

19\. \[Commit the Change](#19-commit-the-change)

20\. \[Create the Merge Request](#20-create-the-merge-request)

21\. \[Merge Commit](#21-merge-commit)

22\. \[Verify the Merge Locally](#22-verify-the-merge-locally)

23\. \[Understanding `git diff` and `A..B`](#23-understanding-git-diff-and-ab)

24\. \[Comparing the Branches](#24-comparing-the-branches)

25\. \[Remote Branches](#25-remote-branches)

26\. \[`git fetch`](#26-git-fetch)

27\. \[`git pull`](#27-git-pull)

28\. \[Deleting the Merged Feature Branch](#28-deleting-the-merged-feature-branch)

29\. \[Final Repository State](#29-final-repository-state)



\*\*Summary\*\*



30\. \[Important Commands](#30-important-commands)

31\. \[Production DevOps Workflow](#31-production-devops-workflow)

32\. \[Key Learnings](#32-key-learnings)

33\. \[Lesson 6 Outcome](#33-lesson-6-outcome)



\---



\## 1. What Is a Merge Request?



A \*\*Merge Request (MR)\*\* is a GitLab feature used to \*\*propose changes from one branch to another branch\*\*.



In a typical development workflow, developers do \*\*not\*\* directly make changes to the `main` branch. Instead:



```text

Developer

&#x20;   ↓

Feature Branch

&#x20;   ↓

Commit Changes

&#x20;   ↓

Push Branch

&#x20;   ↓

Create Merge Request

&#x20;   ↓

Code Review

&#x20;   ↓

CI/CD Validation

&#x20;   ↓

Merge

&#x20;   ↓

Main Branch

```



> The Merge Request acts as a \*\*controlled gateway\*\* between development work and the `main` branch.



\---



\## 2. Real-Life Analogy



Consider a company where an employee wants to change an official document. The employee does not directly modify the official document. Instead, they:



1\. Create a copy.

2\. Make changes in the copy.

3\. Submit the updated copy for review.

4\. Another person reviews the changes.

5\. The changes are approved.

6\. The changes are merged into the official document.



GitLab Merge Requests work in a similar way:



| Real Life | GitLab |

|---|---|

| Working copy | Feature branch |

| Review request | Merge Request |

| Official version | `main` branch |



\---



\## 3. Why Merge Requests Are Important



Merge Requests provide a structured way to manage changes. They support:



\- Code review

\- Collaboration

\- Change tracking

\- Discussion

\- Approval workflows

\- CI/CD validation

\- Controlled merging

\- Better repository history

\- Safer changes to the `main` branch



In a professional DevOps environment, Merge Requests are commonly used as a \*\*control point\*\* before code reaches protected branches.



\---



\## 4. Source Branch and Target Branch



Every Merge Request has two important branches.



| Branch | Meaning | Example |

|---|---|---|

| \*\*Source\*\* | The branch containing the changes | `feature/lesson6-merge-request` |

| \*\*Target\*\* | The branch the changes will be merged into | `main` |



```text

Source: feature/lesson6-merge-request

&#x20;                 ↓

Target: main

```



\---



\## 5. Merge Request vs Git Merge



These concepts are related but \*\*not identical\*\*.



\### Git Merge



`git merge` is a \*\*Git operation\*\* used to combine branch histories:



```bash

git merge feature/login

```



\### GitLab Merge Request



A Merge Request is a \*\*GitLab collaboration and review mechanism\*\* around a proposed change. It can provide:



\- Change comparison

\- Code review

\- Comments

\- Approvals

\- Pipeline results

\- Merge controls

\- Discussion history



| | What it is |

|---|---|

| `git merge` | A Git operation |

| Merge Request | A GitLab collaboration / review workflow |



\---



\## 6. Merge Request Title



The title should clearly describe the purpose of the change.



```text

Add user authentication

```



For this lesson:



```text

Add Lesson 6 merge request practice

```



A clear title helps reviewers quickly understand what the Merge Request is about.



\---



\## 7. Merge Request Description



The description explains:



\- What was changed

\- Why it was changed

\- How it was tested

\- Any important implementation details

\- Any known limitations



Example description:



```markdown

\## Changes



\- Added Lesson 6 practice file

\- Demonstrated GitLab Merge Request workflow

\- Verified the merged content locally



\## Testing



Verified the file exists after merging into main.

```



\---



\## 8. Merge Request Changes / Diff



GitLab provides a \*\*Changes\*\* view where the proposed modifications can be inspected:



```diff

\+ Lesson 6 - GitLab Merge Request Practice

```



The reviewer can inspect exactly what has been \*\*added, removed, or modified\*\*. This is one of the most important parts of code review.



\---



\## 9. Code Review



A reviewer can inspect the changes and provide feedback. The reviewer may:



\- Add comments

\- Ask questions

\- Request modifications

\- Approve the changes

\- Reject the proposed implementation



The developer can then push new commits to the \*\*same feature branch\*\*, and the Merge Request automatically reflects them.



\---



\## 10. Merge Request Lifecycle



```text

Create Feature Branch

&#x20;       ↓

Develop

&#x20;       ↓

Commit

&#x20;       ↓

Push

&#x20;       ↓

Create Merge Request

&#x20;       ↓

Review

&#x20;       ↓

Fix Feedback

&#x20;       ↓

Run CI/CD

&#x20;       ↓

Approve

&#x20;       ↓

Merge

&#x20;       ↓

Delete Feature Branch

```



\---



\## 11. Draft Merge Requests



A \*\*Draft\*\* Merge Request indicates that the work is \*\*not ready for final merging\*\*. It can be used when:



\- Development is still in progress

\- Feedback is required

\- The developer wants early review

\- CI/CD needs to be tested before final completion



A Draft MR can later be \*\*marked as ready\*\* for review.



\---



\## 12. Merge Request Approvals



Organizations can configure approval requirements:



```text

Developer

&#x20;   ↓

Create MR

&#x20;   ↓

Reviewer

&#x20;   ↓

Approval

&#x20;   ↓

Merge

```



This is especially useful for production repositories where changes require review before entering protected branches.



\---



\## 13. CI/CD and Merge Requests



A pipeline can automatically run when a Merge Request is created or updated:



```text

Merge Request

&#x20;     ↓

CI Pipeline

&#x20;     ↓

Build

&#x20;     ↓

Unit Tests

&#x20;     ↓

Code Quality

&#x20;     ↓

Security Checks

&#x20;     ↓

Pipeline Result

&#x20;     ↓

Merge

```



This allows teams to \*\*validate changes before merging\*\* them into the `main` branch.



\---



\## 14. Merge Conflicts



A \*\*merge conflict\*\* occurs when Git cannot automatically combine changes from different branches — for example, when both branches modify the same lines of the same file:



```text

main    ──── modifies lines 10–15 of application.py

&#x20;                         ⚡ conflict

feature ──── modifies lines 10–15 of application.py

```



Git then requires the developer to resolve the conflict manually.



Typical workflow:



```text

Identify Conflict

&#x20;     ↓

Review Conflicting Files

&#x20;     ↓

Resolve Changes

&#x20;     ↓

Stage Files

&#x20;     ↓

Commit Resolution

&#x20;     ↓

Push

&#x20;     ↓

Re-run Validation

&#x20;     ↓

Merge

```



\---



\## 15. Merged vs Closed



| Outcome | Meaning |

|---|---|

| \*\*Merged\*\* | The changes were integrated into the target branch |

| \*\*Closed\*\* | The MR was closed \*\*without\*\* merging the changes |



For this lesson, the Merge Request was successfully \*\*merged\*\*.



\---



\# 🛠️ Hands-On



\## 16. Repository Setup



| Item | Value |

|---|---|

| GitLab repository | `gitlab.com/kaushalsingh1715/gitlab-zero-to-production` |

| Local clone | `C:\\Users\\ASPL-PUNE\\gitlab-zero-to-production-gitlab` |

| Clone method | HTTPS |



\---



\## 17. Create Feature Branch



```bash

git switch -c feature/lesson6-merge-request

```



The purpose of this branch was to demonstrate a real GitLab Merge Request workflow.



\---



\## 18. Create Practice File



File created: `lesson6.txt`



Content:



```text

Lesson 6 - GitLab Merge Request Practice

```



\---



\## 19. Commit the Change



```bash

git add lesson6.txt

git commit -m "Add Lesson 6 merge request practice"

```



Resulting commit:



```text

eb4b619 Add Lesson 6 merge request practice

```



\---



\## 20. Create the Merge Request



A GitLab Merge Request was created with:



| Field | Value |

|---|---|

| Source branch | `feature/lesson6-merge-request` |

| Target branch | `main` |

| Title | `Add Lesson 6 merge request practice` |



The Merge Request was then \*\*merged successfully\*\*. ✅



\---



\## 21. Merge Commit



After the Merge Request was merged, GitLab created a \*\*merge commit\*\*:



```text

92c5537 Merge branch 'feature/lesson6-merge-request' into 'main'

```



The resulting history:



```text

aaa9991 (Initial commit)

&#x20;  │ \\

&#x20;  │  eb4b619 (Add Lesson 6 merge request practice)  ← feature branch

&#x20;  │ /

92c5537 (Merge commit)  ← main

```



The merge commit `92c5537` has \*\*two parents\*\*: `aaa9991` (previous `main`) and `eb4b619` (the feature branch commit). This demonstrates how a Merge Request results in a merge commit.



\---



\## 22. Verify the Merge Locally



```bash

git fetch origin

git status

git log --oneline --graph --all -10

```



Output:



```text

\*   92c5537 (HEAD -> main, origin/main, origin/HEAD) Merge branch 'feature/lesson6-merge-request' into 'main'

|\\

| \* eb4b619 (origin/feature/lesson6-merge-request) Add Lesson 6 merge request practice

|/

\* aaa9991 Initial commit

```



This confirmed that:



\- ✅ The feature branch contained the Lesson 6 commit.

\- ✅ The feature branch was merged into `main`.

\- ✅ `main` contained the merge commit.

\- ✅ The repository was clean and synchronized with GitLab.



\---



\## 23. Understanding `git diff` and `A..B`



The following command was executed:



```bash

git diff main..origin/feature/lesson6-merge-request

```



\*\*No output was produced.\*\* This was expected.



> ⚠️ \*\*Important:\*\* `A..B` means different things for `git diff` and `git log`.



| Command | What `A..B` means |

|---|---|

| `git diff A..B` | Compare the \*\*file contents\*\* at the tip of `A` with the tip of `B` (same as `git diff A B`) |

| `git log A..B` | Show \*\*commits\*\* reachable from `B` that are \*\*not\*\* reachable from `A` |



Why `git diff` was empty: after the merge, the files in `main` are identical to the files in the feature branch — `main` gained `lesson6.txt` and nothing else changed. Same contents → no differences.



Similarly:



```bash

git log --oneline main..origin/feature/lesson6-merge-request

```



returned \*\*no output\*\*, because every commit on the feature branch is already part of `main`.



\---



\## 24. Comparing the Branches



Reversing the direction:



```bash

git log --oneline origin/feature/lesson6-merge-request..main

```



Output:



```text

92c5537 Merge branch 'feature/lesson6-merge-request' into 'main'

```



This shows that `main` contains the \*\*merge commit\*\*, which is not present on the feature branch.



\---



\## 25. Remote Branches



The repository initially showed:



```text

origin/HEAD -> origin/main

origin/feature/lesson6-merge-request

origin/main

```



This demonstrates the difference between local branches and remote-tracking branches:



| Type | Branches |

|---|---|

| Local | `main` |

| Remote-tracking | `origin/main`, `origin/feature/lesson6-merge-request` |



\---



\## 26. `git fetch`



```bash

git fetch origin

```



`git fetch` retrieves the latest information from the remote repository and \*\*updates remote-tracking references\*\* (like `origin/main`) \*\*without\*\* changing your current working branch or files.



> 💡 Think of `git fetch` as \*"check what's new on GitLab"\* — it looks, but doesn't touch your work.



\---



\## 27. `git pull`



`git pull` is essentially:



```text

git fetch  +  git merge   (or git rebase, depending on configuration)

```



Example:



```bash

git pull origin main

```



This retrieves changes from the remote \*\*and integrates them\*\* into the current branch.



| Command | Downloads changes | Changes your current branch |

|---|---|---|

| `git fetch` | ✅ | ❌ |

| `git pull` | ✅ | ✅ |



\---



\## 28. Deleting the Merged Feature Branch



After the Merge Request was completed, the feature branch was deleted from the remote:



```bash

git push origin --delete feature/lesson6-merge-request

```



Stale remote-tracking references were then cleaned up:



```bash

git fetch --prune

```



`--prune` removes remote-tracking references for branches that \*\*no longer exist\*\* on the remote.



> 💡 GitLab also offers a \*\*"Delete source branch"\*\* checkbox when merging an MR, which does this automatically.



\---



\## 29. Final Repository State



```bash

git branch -r

```



```text

origin/HEAD -> origin/main

origin/main

```



The temporary Lesson 6 feature branch was no longer required. 🧹



\---



\# 📋 Summary



\## 30. Important Commands



| Command | Purpose |

|---|---|

| `git switch -c <branch>` | Create and switch to a branch |

| `git add <file>` | Stage changes |

| `git commit -m "message"` | Create a commit |

| `git push -u origin <branch>` | Push a new branch |

| `git fetch origin` | Fetch remote information |

| `git pull origin main` | Fetch and integrate changes |

| `git log --oneline --graph --all` | View commit history as a graph |

| `git log A..B` | Commits in `B` that are not in `A` |

| `git diff A B` | Compare file contents between two branches/commits |

| `git branch -r` | View remote branches |

| `git push origin --delete <branch>` | Delete a remote branch |

| `git fetch --prune` | Remove stale remote-tracking references |



\---



\## 31. Production DevOps Workflow



```text

Developer

&#x20;   │

&#x20;   v

GitLab Repository

&#x20;   │

&#x20;   v

Feature Branch

&#x20;   │

&#x20;   v

Development → Commit → Push

&#x20;   │

&#x20;   v

Merge Request

&#x20;   ├── Code Review

&#x20;   ├── Automated Tests

&#x20;   ├── Code Quality

&#x20;   └── Security Checks

&#x20;   │

&#x20;   v

Approval

&#x20;   │

&#x20;   v

Merge

&#x20;   │

&#x20;   v

Main Branch

&#x20;   │

&#x20;   v

CI/CD Pipeline

&#x20;   │

&#x20;   v

Build / Test / Package

&#x20;   │

&#x20;   v

Artifact Repository

&#x20;   │

&#x20;   v

Deployment

```



Later lessons will connect this workflow with:



\- GitLab CI/CD

\- Jenkins

\- SonarQube

\- JFrog Artifactory

\- Docker

\- Terraform

\- Kubernetes



\---



\## 32. Key Learnings



\- \[x] What a GitLab Merge Request is

\- \[x] Why Merge Requests are used

\- \[x] Source branch vs target branch

\- \[x] Merge Request vs `git merge`

\- \[x] Code review

\- \[x] MR comments

\- \[x] MR approvals

\- \[x] Draft Merge Requests

\- \[x] CI/CD integration with Merge Requests

\- \[x] Merge conflicts

\- \[x] Merged vs closed

\- \[x] Merge commits

\- \[x] Remote-tracking branches

\- \[x] `git fetch`

\- \[x] `git pull`

\- \[x] `git diff`

\- \[x] Branch comparison using `A..B`

\- \[x] Remote branch deletion

\- \[x] `git fetch --prune`



\---



\## 33. Lesson 6 Outcome



A complete GitLab Merge Request workflow was successfully performed:



```text

Feature Branch

&#x20;     ↓

Commit

&#x20;     ↓

Push

&#x20;     ↓

Merge Request

&#x20;     ↓

Review

&#x20;     ↓

Merge

&#x20;     ↓

Merge Commit

&#x20;     ↓

Verify Locally

&#x20;     ↓

Delete Feature Branch

&#x20;     ↓

Prune Remote References

```



This provides the foundation for the next GitLab collaboration topics and, eventually, GitLab CI/CD.



\---



\### ✅ Status: Lesson 6 — Completed





