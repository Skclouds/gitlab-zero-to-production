\# Lesson 4 — GitLab Project \& Repository Management



\## Objective



The objective of this lesson is to understand how Git repositories are managed locally and remotely using GitLab.



\### Topics Covered



\- GitLab Projects and Repositories

\- Local and Remote Repositories

\- Cloning a GitLab Repository

\- Git Remotes

\- The `origin` Remote

\- Git Push and Pull Operations

\- Git Fetch vs Git Pull

\- Local and Remote Branches

\- Repository Files

\- `.gitignore`

\- `.gitattributes`

\- Git Tags

\- GitLab Releases

\- Local-to-Remote Git Workflow

\- Real-World DevOps Repository Workflow



\---



\# 1. GitLab Project vs Repository



A \*\*GitLab Project\*\* is the complete workspace for a software project.



A GitLab Project can contain:



\- Git Repository

\- Issues

\- Merge Requests

\- CI/CD Pipelines

\- Wiki

\- Project Members

\- Package Registry

\- Container Registry

\- Security Features

\- Project Settings



The \*\*Git Repository\*\* is the part of the GitLab Project that stores the project's source code and version history.



A repository contains:



\- Source Code

\- Files

\- Commits

\- Branches

\- Tags

\- Version History



\### Simple Example



Think of a \*\*GitLab Project as a company office\*\*.



The \*\*Git Repository is the file/document storage inside that office\*\*.



The project contains the repository along with collaboration, CI/CD, security, and project-management features.



\---



\# 2. Local Repository vs Remote Repository



A Git repository can exist in two important locations:



1\. Local Repository

2\. Remote Repository



\## 2.1 Local Repository



A \*\*local repository\*\* is the Git repository stored on the developer's computer.



It contains the project's Git history and allows developers to work on the project locally.



Example:



```text

C:\\Users\\ASPL-PUNE\\gitlab-zero-to-production



Developers can create commits, create branches, inspect history, and perform other Git operations locally.



2.2 Remote Repository



A remote repository is a repository hosted on a remote Git server such as GitLab.



Example:



git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git



The remote repository acts as a shared repository for the development team.



3\. Git Repository Workflow



The basic Git workflow can be represented as:



Working Directory

&#x20;      |

&#x20;      | git add

&#x20;      v

Staging Area

&#x20;      |

&#x20;      | git commit

&#x20;      v

Local Repository

&#x20;      |

&#x20;      | git push

&#x20;      v

Remote Repository

&#x20;      |

&#x20;    GitLab



Changes from GitLab can also be retrieved into the local repository using:



git fetch



or:



git pull

4\. Cloning a GitLab Repository



Cloning creates a local copy of a remote GitLab repository.



Example:



git clone git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git



A cloned repository normally contains:



Project files

Commit history

Branch information

Git metadata

Remote configuration



The clone allows developers to work on the project locally.



Example

git clone git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git



Navigate into the repository:



cd gitlab-zero-to-production



Check the repository status:



git status

5\. Git Remote



A Git remote is a reference to another Git repository.



In this learning project, the remote points to the GitLab repository.



To view the configured remotes:



git remote -v



Example:



origin  git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git (fetch)

origin  git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git (push)



The output shows the remote repository URL used for:



Fetching changes

Pushing changes

6\. What Is origin?



origin is the conventional default name Git assigns to the remote repository when a repository is cloned.



Important

origin != GitLab



origin is simply a nickname/reference for a remote repository.



For example:



origin

&#x20;  |

&#x20;  v

GitLab Repository



A remote repository can have another name as well.



Example:



git remote add upstream <repository-url>



In this case, upstream becomes another remote name.



7\. Git Push



The git push command sends local commits to a remote repository.



Example:



git push origin main



The command can be understood as:



git push

&#x20;    |

&#x20;    +-- origin = remote repository

&#x20;    |

&#x20;    +-- main = branch

Workflow

Local Commit

&#x20;    |

&#x20;    | git push

&#x20;    v

GitLab Repository

Example

git add .

git commit -m "Add repository management practice"

git push origin main



After a successful push, the commit becomes available in the remote GitLab repository.



8\. Git Pull



The git pull command retrieves changes from the remote repository and integrates them into the current local branch.



Example:



git pull origin main



Conceptually:



GitLab

&#x20;  |

&#x20;  | Fetch Changes

&#x20;  v

Local Repository

&#x20;  |

&#x20;  | Integrate Changes

&#x20;  v

Current Branch



git pull is commonly used when other developers have pushed changes to the remote repository and you want to bring those changes into your current branch.



9\. Git Fetch



The git fetch command downloads changes from the remote repository without automatically integrating those changes into the current working branch.



Example:



git fetch origin



Conceptually:



GitLab

&#x20;  |

&#x20;  | git fetch

&#x20;  v

Local Remote-Tracking Information



The current working branch is not automatically changed by git fetch.



This makes fetch useful when you want to inspect remote changes before deciding how to integrate them.



10\. Git Fetch vs Git Pull

Command	Purpose

git fetch	Downloads remote changes without integrating them into the current branch

git pull	Fetches remote changes and integrates them into the current branch

Simple Way to Remember

fetch = "bring information"



pull = "bring + integrate"

11\. Checking Local and Remote Branches



Git provides commands to inspect local and remote branches.



View Local Branches

git branch

View Remote Branches

git branch -r

View Both Local and Remote Branches

git branch -a



This is useful for understanding the relationship between local branches and branches available on the remote repository.



12\. Changing a Remote URL



A configured remote URL can be changed using:



git remote set-url origin <new-url>



Example:



git remote set-url origin git@gitlab.com:kaushalsingh1715/gitlab-zero-to-production.git



Verify the updated remote:



git remote -v

13\. README.md



README.md is commonly used to document a project.



A README can contain:



Project Overview

Installation Instructions

Usage Instructions

Technologies Used

Architecture

Configuration

CI/CD Information

Contribution Instructions



Example:



README.md



GitLab can display the README directly on the project repository page.



14\. .gitignore



The .gitignore file tells Git which files and directories should normally not be tracked.



Example:



node\_modules/

.env

\*.log

target/

\_\_pycache\_\_/



Common examples of files that should not normally be committed include:



Environment files

Secrets

Password files

Temporary files

Build output

Dependency directories

Log files

Example



An .env file may contain sensitive configuration such as:



DATABASE\_PASSWORD=\*\*\*\*\*\*

API\_KEY=\*\*\*\*\*\*



Sensitive information such as passwords, API keys, and credentials should not normally be committed to a Git repository.



15\. .gitattributes



The .gitattributes file is used to define attributes and handling rules for files and paths within a Git repository.



Example:



\*.sh text eol=lf

\*.bat text eol=crlf

\*.png binary



It can help Git determine how particular files should be handled.



.gitignore vs .gitattributes

.gitignore

&#x20;   |

&#x20;   +-- Controls which files Git should normally ignore





.gitattributes

&#x20;   |

&#x20;   +-- Controls attributes and handling of files

Key Difference

File	Purpose

.gitignore	Defines files and directories that Git should normally ignore

.gitattributes	Defines attributes and handling rules for files and paths

16\. Git Tags



A Git tag is a reference that marks an important point in a repository's history.



Tags are commonly used to identify software versions.



Examples:



v1.0.0

v1.1.0

v2.0.0

16.1 Create a Lightweight Tag

git tag v1.0.0

16.2 Create an Annotated Tag

git tag -a v1.0.0 -m "Release version 1.0.0"



Annotated tags can contain additional information such as a message and tag metadata.



16.3 List Tags

git tag

16.4 Push a Tag

git push origin v1.0.0

16.5 Push All Tags

git push origin --tags

17\. GitLab Releases



A GitLab Release represents a specific version of a software project.



Releases are commonly associated with Git tags.



Example:



Git Commit

&#x20;    |

&#x20;    v

Git Tag

v1.0.0

&#x20;    |

&#x20;    v

GitLab Release

Version 1.0.0



A release can be used to communicate a specific software version and its associated changes.



For example:



Version: v1.0.0



Release:

\- Initial production version

\- Added authentication

\- Added database integration

\- Fixed application startup issue

18\. Important Git Commands

Command	Purpose

git clone	Creates a local copy of a remote repository

git status	Shows the current repository state

git remote -v	Displays configured remote URLs

git branch	Displays local branches

git branch -r	Displays remote branches

git add	Stages changes

git commit	Creates a local commit

git push	Sends commits to a remote repository

git fetch	Downloads remote changes without integrating them

git pull	Fetches and integrates remote changes

git log	Displays commit history

git tag	Creates or lists Git tags

19\. Real-World DevOps Example



Consider a development team working on an application.



Developer A

&#x20;    |

&#x20;    | git push

&#x20;    v

GitLab Repository

&#x20;    ^

&#x20;    |

&#x20;    | git pull

&#x20;    |

Developer B

Developer A



Developer A develops a new feature and pushes the changes:



git add .

git commit -m "Add new feature"

git push origin main



The changes are now available in the GitLab repository.



Developer B



Developer B can retrieve the latest changes using:



git pull origin main



For safer inspection of remote changes, Developer B can first use:



git fetch origin



The remote changes can then be inspected before deciding how to integrate them.



DevOps Perspective



In a production DevOps environment, the GitLab repository can act as the central source of truth for application source code and the starting point for CI/CD automation.



A typical workflow can look like:



Developer

&#x20;   |

&#x20;   v

GitLab Repository

&#x20;   |

&#x20;   v

CI/CD Pipeline

&#x20;   |

&#x20;   +---- Build

&#x20;   |

&#x20;   +---- Test

&#x20;   |

&#x20;   +---- Code Quality

&#x20;   |

&#x20;   +---- Security Checks

&#x20;   |

&#x20;   v

Artifact / Container

&#x20;   |

&#x20;   v

Deployment

20\. Key Takeaways



After completing this lesson, the following concepts should be understood:



A GitLab Project can contain a Git repository and other DevOps features.

A repository stores source code and version history.

A local repository exists on the developer's machine.

A remote repository can be hosted on GitLab.

origin is the conventional name for a remote repository.

git clone creates a local copy of a remote repository.

git push sends local commits to a remote repository.

git pull retrieves and integrates remote changes.

git fetch retrieves remote information without automatically integrating it into the current branch.

.gitignore defines files and directories that Git should normally ignore.

.gitattributes defines attributes and handling rules for files.

Git tags identify important points in repository history.

GitLab Releases can represent formal software versions.

Git repositories provide the foundation for collaborative development and CI/CD workflows.

21\. Lesson 4 Status

Concepts Completed

&#x20;GitLab Project vs Repository

&#x20;Local vs Remote Repository

&#x20;Git Clone

&#x20;Git Remote

&#x20;origin

&#x20;Git Push

&#x20;Git Pull

&#x20;Git Fetch

&#x20;Local and Remote Branches

&#x20;README.md

&#x20;.gitignore

&#x20;.gitattributes

&#x20;Git Tags

&#x20;GitLab Releases

&#x20;Repository Workflow

&#x20;Real-World DevOps Example

