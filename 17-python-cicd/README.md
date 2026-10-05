# Lesson 17 — Python CI/CD with GitLab

## 🎯 Overview

This lesson builds a **Python CI/CD pipeline** using GitLab CI/CD and the self-managed Windows GitLab Runner from Lesson 14.

The implementation covers:

- Python project structure
- Python application development
- Unit testing with pytest
- Python dependency management
- GitLab CI/CD pipeline creation
- Self-managed Windows Runner execution
- Python environment configuration for the Runner
- GitLab CI/CD cache
- JUnit test reporting
- Python package building
- CI/CD troubleshooting
- Production-oriented Python CI practices

---

## 📚 Table of Contents

**Setup**

1. [Learning Objectives](#1-learning-objectives)
2. [Project Architecture](#2-project-architecture)
3. [Python Application](#3-python-application)
4. [Python Environment](#4-python-environment)
5. [Dependency Management](#5-dependency-management)
6. [Unit Testing with pytest](#6-unit-testing-with-pytest)
7. [Python Project Working Directory](#7-python-project-working-directory)

**GitLab CI/CD**

8. [Initial GitLab CI Pipeline](#8-initial-gitlab-ci-pipeline)
9. [GitLab Runner](#9-gitlab-runner)
10. [GitLab Runner Python PATH Issue](#10-gitlab-runner-python-path-issue)
11. [Why `python -m` Was Used](#11-why-python--m-was-used)
12. [GitLab CI Dependency Cache](#12-gitlab-ci-dependency-cache)
13. [Cache vs Artifact](#13-cache-vs-artifact)
14. [JUnit Test Reporting](#14-junit-test-reporting)
15. [Python Package Building](#15-python-package-building)
16. [Consolidated `.gitlab-ci.yml`](#16-consolidated-gitlab-ciyml)
17. [Python CI/CD Pipeline Flow](#17-python-cicd-pipeline-flow)

**Troubleshooting & Practices**

18. [Troubleshooting Performed](#18-troubleshooting-performed)
19. [Git Best Practices Practiced](#19-git-best-practices-practiced)
20. [Production-Oriented Practices](#20-production-oriented-practices)

**Summary**

21. [Key Commands](#21-key-commands)
22. [Final Outcome](#22-final-outcome)
23. [Lesson Status](#23-lesson-status)

---

## 1. Learning Objectives

By the end of this lesson, the following were practiced:

1. Create a Python application structure
2. Create unit tests using pytest
3. Manage Python dependencies using `requirements.txt`
4. Execute Python tests locally
5. Create a GitLab CI/CD pipeline
6. Execute the pipeline on a self-managed Windows Runner
7. Make Python available to the GitLab Runner service
8. Use `python -m pip` and `python -m pytest`
9. Configure GitLab CI dependency caching
10. Generate JUnit test reports
11. Build a Python distribution package
12. Troubleshoot real CI/CD failures

---

## 2. Project Architecture

The Python application lives inside the GitLab hands-on repository, next to the Java project from Lesson 16:

```text
gitlab-zero-to-production/
│
├── .gitlab-ci.yml
│
└── student-api/
    │
    ├── requirements.txt
    ├── pyproject.toml
    │
    ├── src/
    │   └── student_api/
    │       ├── __init__.py
    │       └── app.py
    │
    └── tests/
        └── test_app.py
```

| Path | Purpose |
|---|---|
| `src/student_api/` | Application code (the Python package) |
| `src/student_api/__init__.py` | Marks the folder as a Python package |
| `tests/` | Test code (not shipped in the package) |
| `requirements.txt` | Dependencies to install |
| `pyproject.toml` | Packaging configuration |

> 💡 This is the **"src layout"** — application code sits under `src/`, separate from tests. It's the layout recommended by the Python Packaging Authority.

---

## 3. Python Application

`student-api/src/student_api/app.py`:

```python
def get_application_name():
    return "Student Management API"


def get_student_count():
    return 10


if __name__ == "__main__":
    print(get_application_name())
    print(f"Student count: {get_student_count()}")
```

The application provides two functions used by the unit tests:

```text
get_application_name()
get_student_count()
```

> 💡 `if __name__ == "__main__":` means the `print` lines run only when the file is executed directly — not when the tests import it.

---

## 4. Python Environment

| Tool | Version |
|---|---|
| Python | 3.14.6 |
| pip | 26.2.1 |
| pytest | 9.1.1 |

Python executable:

```text
C:\Users\ASPL-PUNE\AppData\Local\Python\pythoncore-3.14-64\python.exe
```

Verified with:

```bash
python --version
python -m pip --version
```

---

## 5. Dependency Management

`student-api/requirements.txt`:

```text
pytest
```

Installed with:

```bash
python -m pip install -r requirements.txt
```

> 💡 Using `python -m pip` instead of plain `pip` guarantees that pip belongs to the **same Python interpreter** you are running.

> ⚠️ **Reproducibility tip:** An unpinned `pytest` installs whatever version is newest on the day the pipeline runs. Pinning it (`pytest==9.1.1`) means every pipeline uses the same version, so a pytest release can't suddenly break your build.

---

## 6. Unit Testing with pytest

`student-api/tests/test_app.py`:

```python
from src.student_api.app import get_application_name, get_student_count


def test_application_name():
    assert get_application_name() == "Student Management API"


def test_student_count():
    assert get_student_count() == 10
```

pytest automatically finds files named `test_*.py` and functions named `test_*`.

```bash
python -m pytest
```

Result:

```text
collected 2 items

tests\test_app.py ..    [100%]

2 passed
```

> 💡 Unlike the Lesson 16 Java test, these tests call **real application functions** — if someone changes `get_student_count()`, the test catches it.

---

## 7. Python Project Working Directory

One important troubleshooting lesson was the **Python import path**.

The tests import:

```python
from src.student_api.app import ...
```

so Python must be able to find a folder called `src`. That works only when pytest runs **from `student-api/`**.

| Command | Result |
|---|---|
| `cd student-api` → `python -m pytest` | ✅ Works |
| `python -m pytest student-api` (from repository root) | ❌ `ModuleNotFoundError: No module named 'src'` |

> 💡 **Why it works:** `python -m pytest` adds the **current directory** to Python's import path. From `student-api/`, that makes the `src` folder importable. Plain `pytest` doesn't do this — which is part of why `pytest` and `python -m pytest` can behave differently.

### 🔧 Cleaner long-term fix

Importing from `src.` works, but it isn't the usual convention. A more robust setup tells pytest where the code lives, in `pyproject.toml`:

```toml
[tool.pytest.ini_options]
pythonpath = ["src"]
testpaths = ["tests"]
```

and imports the package by its real name:

```python
from student_api.app import get_application_name, get_student_count
```

Now the tests don't depend on running from one specific folder.

---

# ⚙️ GitLab CI/CD

## 8. Initial GitLab CI Pipeline

```yaml
stages:
  - test

python-tests:
  stage: test
  tags:
    - windows
  script:
    - cd student-api
    - python --version
    - python -m pip --version
    - python -m pip install -r requirements.txt
    - python -m pytest
```

---

## 9. GitLab Runner

The pipeline uses the self-managed Windows Runner from Lesson 14:

| Setting | Value |
|---|---|
| Runner | `kaushal-windows-runner` |
| Tag | `windows` |
| Executor | `shell` |
| Shell | PowerShell (`powershell`) |

Selected with:

```yaml
tags:
  - windows
```

---

## 10. GitLab Runner Python PATH Issue

The first Python pipeline **failed**:

```text
python : The term 'python' is not recognized
```

But Python worked fine from the normal user CMD. 🤔

### 🔍 Cause

| Environment | Uses |
|---|---|
| Your CMD window | **Your user** PATH + System PATH |
| GitLab Runner service | Runs as **Local System** → only the **System** PATH |

Python was installed under your **user profile** (`AppData\Local\...`), and its folder was only on your **user** PATH. The Runner service runs as a different account, so it never saw it.

### ✅ Solution

1. Add the Python directories to the Windows **System** PATH:
   ```text
   C:\Users\ASPL-PUNE\AppData\Local\Python\pythoncore-3.14-64
   C:\Users\ASPL-PUNE\AppData\Local\Python\pythoncore-3.14-64\Scripts
   ```
2. Restart the Runner so it picks up the new PATH (from an **administrator** prompt):
   ```cmd
   gitlab-runner-windows-amd64.exe restart
   gitlab-runner-windows-amd64.exe status
   ```
   ```text
   gitlab-runner: Service is running
   ```
3. Verify in the job log:
   ```text
   Python 3.14.6
   ```

> 💡 A Windows service reads environment variables **only when it starts** — that's why the restart is required.

> 🏭 **Production note:** Pointing the System PATH at one user's `AppData` folder works, but it ties the Runner to that user's profile. For a shared Runner machine, installing Python **for all users** (e.g. under `C:\Program Files`) is cleaner.

---

## 11. Why `python -m` Was Used

| Instead of | The pipeline uses | Why |
|---|---|---|
| `pytest` | `python -m pytest` | Works even if the `pytest.exe` script folder isn't on PATH; also adds the current directory to the import path |
| `pip install` | `python -m pip install` | Guarantees pip matches the Python interpreter being used |

---

## 12. GitLab CI Dependency Cache

pip caching was configured to speed up pipelines:

```yaml
variables:
  PIP_CACHE_DIR: "$CI_PROJECT_DIR/.cache/pip"

cache:
  key: pip
  paths:
    - .cache/pip
```

```text
First Pipeline
      ↓
Download dependencies
      ↓
Store pip cache
      ↓
Pipeline completes

Next Pipeline
      ↓
Restore cache
      ↓
Reuse cached packages
      ↓
Faster dependency installation ⚡
```

> ✅ `$CI_PROJECT_DIR` makes the path absolute, so it matches the cache path even after `cd student-api` (the same trap as Lesson 16's Maven cache).

> 💡 **Cache key:** If Java and Python jobs live in the **same** `.gitlab-ci.yml`, give each cache its own `key:` (e.g. `pip` and `maven`). Jobs with the same key share one cache, and can overwrite each other's contents.

---

## 13. Cache vs Artifact

| Feature | Cache | Artifact |
|---|---|---|
| Primary purpose | Speed up future jobs | Preserve job output |
| Example | pip cache | Python package, test report |
| Usage | Reuse dependencies | Download / share output |
| Lifecycle | Temporary, best effort | Kept until expiry |
| Downloadable from GitLab UI | ❌ | ✅ |

---

## 14. JUnit Test Reporting

pytest can write a JUnit-compatible XML report:

```yaml
- python -m pytest --junitxml=pytest-report.xml
```

GitLab reads it with:

```yaml
artifacts:
  when: always
  reports:
    junit:
      - student-api/pytest-report.xml
```

```text
pytest
   ↓
pytest-report.xml
   ↓
GitLab CI
   ↓
JUnit Test Report
   ↓
GitLab Test Results (Tests tab)
```

> ⚠️ `when: always` ensures the report is collected **even when tests fail** — which is when you need it most.

> 💡 The report path is relative to the **repository root** (`student-api/pytest-report.xml`), even though pytest ran inside `student-api/`.

---

## 15. Python Package Building

The project was prepared for packaging with `pyproject.toml`:

```toml
[build-system]
requires = ["setuptools>=61"]
build-backend = "setuptools.build_meta"

[project]
name = "student-management-api"
version = "1.0.0"
description = "Student Management API"
requires-python = ">=3.14"
```

Install the build tool and build:

```bash
python -m pip install build
python -m build
```

Output in `dist/`:

| File | Type | Purpose |
|---|---|---|
| `*.whl` | **Wheel** (built distribution) | Ready to install quickly with pip |
| `*.tar.gz` | **sdist** (source distribution) | Source package, built on install |

> ☕ **Java comparison:** The `.whl` file plays the same role as the `.jar` from Lesson 16 — the packaged, shippable output of the build.

---

## 16. Consolidated `.gitlab-ci.yml`

All pieces from this lesson in one file, including a package job that saves the wheel as an artifact:

```yaml
variables:
  PIP_CACHE_DIR: "$CI_PROJECT_DIR/.cache/pip"

stages:
  - test
  - package

default:
  tags:
    - windows
  cache:
    key: pip
    paths:
      - .cache/pip
  before_script:
    - cd student-api
    - python --version
    - python -m pip --version
    - python -m pip install -r requirements.txt

python-tests:
  stage: test
  script:
    - python -m pytest --junitxml=pytest-report.xml
  artifacts:
    when: always
    reports:
      junit:
        - student-api/pytest-report.xml

python-package:
  stage: package
  script:
    - python -m pip install build
    - python -m build
  artifacts:
    paths:
      - student-api/dist/
    expire_in: 1 week
```

---

## 17. Python CI/CD Pipeline Flow

```text
Developer
    │
    ▼
GitLab Repository
    │
    ▼
GitLab CI Pipeline
    │
    ▼
Windows GitLab Runner
    │
    ├── Python version
    ├── pip version
    ├── Restore pip cache
    ├── Install dependencies
    ├── Run pytest
    ├── Generate JUnit report
    └── Build Python package
    │
    ▼
Pipeline Result
```

---

# 🔧 Troubleshooting & Practices

## 18. Troubleshooting Performed

| # | Error | Cause | Solution |
|---|---|---|---|
| 1 | `collected 0 items` | `tests/` folder was empty | Created `tests/test_app.py` |
| 2 | `'pytest' is not recognized as an internal or external command` | pytest's `Scripts` folder not on PATH | Use `python -m pytest` |
| 3 | `ModuleNotFoundError: No module named 'src'` | pytest ran from the repository root | `cd student-api` before running pytest |
| 4 | `python : The term 'python' is not recognized` (in CI) | Python was on the **user** PATH, but the Runner service only sees the **System** PATH | Add Python to System PATH, restart Runner, verify |

> 🧠 **Lesson from problem 4:** *"Works on my machine"* is not the same as *"works on the Runner."* The Runner is a separate environment, often running as a different account. Always troubleshoot the **actual CI environment**.

---

## 19. Git Best Practices Practiced

Before committing, the staging area was inspected:

```bash
git status
git diff --cached --stat
git diff --cached --name-only
```

Generated files were excluded with `.gitignore`:

```gitignore
# Java
student-management/target/

# Python
__pycache__/
*.pyc
.pytest_cache/
pytest-report.xml
dist/
build/
*.egg-info/

# CI caches
.cache/
.m2/
```

> 💡 A pattern like `__pycache__/` (without a folder prefix) matches **at any depth** — so it also covers `src/student_api/__pycache__/` and `tests/__pycache__/`, which `student-api/__pycache__/` alone would miss.

---

## 20. Production-Oriented Practices

- ✅ Use `python -m pip` and `python -m pytest`
- ✅ Keep application source separate from tests (src layout)
- ✅ Use `requirements.txt` for dependencies — and pin versions
- ✅ Use `.gitignore` for generated files
- ✅ Use a self-managed GitLab Runner with tags
- ✅ Configure dependency caching (with an absolute path and a cache key)
- ✅ Publish JUnit test reports
- ✅ Generate build artifacts/packages
- ✅ Inspect Git staging before committing
- ✅ Troubleshoot the actual CI environment rather than assuming it matches the local one

> 🏭 **Shell executor note:** On a Shell Runner, `pip install` installs packages into the Runner machine's **global** Python, shared by every job and project. In production, jobs often create a fresh virtual environment first (`python -m venv .venv`) — or use a **Docker** executor, where each job gets a clean Python image.

---

# 📋 Summary

## 21. Key Commands

| Command | Purpose |
|---|---|
| `python --version` | Check Python |
| `python -m pip --version` | Check pip |
| `python -m pip install -r requirements.txt` | Install dependencies |
| `python -m pytest` | Run tests |
| `python -m pytest --junitxml=pytest-report.xml` | Run tests and generate JUnit report |
| `python -m pip install build` | Install the Python build tool |
| `python -m build` | Build wheel and sdist into `dist/` |
| `gitlab-runner-windows-amd64.exe restart` | Restart Runner (picks up new PATH) |
| `git status` | Check Git status |
| `git diff --cached --name-only` | List staged files |
| `git diff --cached --stat` | Summarize staged changes |

---

## 22. Final Outcome

Lesson 17 established a working **Python CI/CD workflow** with GitLab:

```text
Python Application
       │
       ▼
GitLab Repository
       │
       ▼
GitLab CI/CD
       │
       ▼
Self-Managed Windows Runner
       ├── Python
       ├── pip
       ├── Dependency Cache
       ├── pytest
       └── JUnit Reports
       │
       ▼
Python Package
```

Test result:

```text
2 passed ✅
```

---

## 23. Lesson Status

| Item | Status |
|---|---|
| Lesson 17 — Python CI/CD | ✅ Completed |
| Hands-on | ✅ Completed |
| GitLab Pipeline | ✅ Passed |
| Python Tests | ✅ 2 passed |
| Dependency Cache | ✅ Configured |
| JUnit Reporting | ✅ Configured |
| Python Packaging | ✅ Practiced |

---

### ✅ Status: Lesson 17 — Completed
