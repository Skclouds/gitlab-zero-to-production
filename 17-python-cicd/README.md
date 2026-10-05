Lesson 17 — Python CI/CD with GitLab



\## Overview



This lesson demonstrates how to build a Python CI/CD pipeline using GitLab CI/CD and a self-managed Windows GitLab Runner.



The implementation covers:



\- Python project structure

\- Python application development

\- Unit testing with pytest

\- Python dependency management

\- GitLab CI/CD pipeline creation

\- Self-managed Windows Runner execution

\- Python environment configuration

\- GitLab CI/CD cache

\- JUnit test reporting

\- Python package building

\- CI/CD troubleshooting

\- Production-oriented Python CI practices



\---



\# 1. Learning Objectives



By the end of this lesson, the following concepts were practiced:



1\. Create a Python application structure.

2\. Create unit tests using pytest.

3\. Manage Python dependencies using `requirements.txt`.

4\. Execute Python tests locally.

5\. Create a GitLab CI/CD pipeline.

6\. Execute the pipeline using a self-managed Windows Runner.

7\. Configure Python availability for the GitLab Runner service.

8\. Use `python -m pip` and `python -m pytest`.

9\. Configure GitLab CI dependency caching.

10\. Generate JUnit test reports.

11\. Build a Python distribution package.

12\. Troubleshoot real CI/CD failures.



\---



\# 2. Project Architecture



The Python application was created inside the GitLab hands-on repository.



```text

gitlab-zero-to-production/

│

├── .gitlab-ci.yml

│

└── student-api/

&#x20;   │

&#x20;   ├── requirements.txt

&#x20;   ├── pyproject.toml

&#x20;   │

&#x20;   ├── src/

&#x20;   │   └── student\_api/

&#x20;   │       ├── \_\_init\_\_.py

&#x20;   │       └── app.py

&#x20;   │

&#x20;   └── tests/

&#x20;       └── test\_app.py



3\. Python Application

app.py

Location:

student-api/src/student\_api/app.py



Implementation:

def get\_application\_name():    return "Student Management API"def get\_student\_count():    return 10if \_\_name\_\_ == "\_\_main\_\_":    print(get\_application\_name())    print(f"Student count: {get\_student\_count()}")





The application provides two simple functions:

get\_application\_name()get\_student\_count()





These functions are used by the unit tests.

4\. Python Environment

The development environment used:

Python: 3.14.6

pip: 26.2.1

pytest: 9.1.1



Python executable:

C:\\Users\\ASPL-PUNE\\AppData\\Local\\Python\\pythoncore-3.14-64\\python.exe



Python was verified using:

python --version



and:

python -m pip --version



5\. Dependency Management

The project uses:

student-api/requirements.txt



Content:

pytest



Dependencies were installed using:

python -m pip install -r requirements.txt



Using:

python -m pip



is preferred over directly calling:

pip



because it ensures that pip belongs to the Python interpreter being used.

6\. Unit Testing with pytest

Test file:

student-api/tests/test\_app.py



Implementation:

from src.student\_api.app import get\_application\_name, get\_student\_countdef test\_application\_name():    assert get\_application\_name() == "Student Management API"def test\_student\_count():    assert get\_student\_count() == 10





Tests were executed using:

python -m pytest



Successful result:

collected 2 items



tests\\test\_app.py ..    \[100%]



2 passed



7\. Python Project Working Directory

One important troubleshooting lesson was the Python import path.

The project structure is:

student-api/

├── src/

└── tests/



The tests import:

from src.student\_api.app import ...





Therefore pytest should be executed from:

student-api/



Correct:

cd student-api

python -m pytest



Running pytest from the repository root with:

python -m pytest student-api



caused:

ModuleNotFoundError: No module named 'src'



The issue was resolved by running pytest from the Python project's directory.

8\. Initial GitLab CI Pipeline

The first Python CI pipeline was created in:

.gitlab-ci.yml



Initial pipeline structure:

stages:

&#x20; - test



python-tests:

&#x20; stage: test

&#x20; tags:

&#x20;   - windows

&#x20; script:

&#x20;   - cd student-api

&#x20;   - python --version

&#x20;   - python -m pip --version

&#x20;   - python -m pip install -r requirements.txt

&#x20;   - python -m pytest



9\. GitLab Runner

The pipeline uses the self-managed Windows Runner created during Lesson 14.

Runner:

kaushal-windows-runner



Tag:

windows



Executor:

shell



Shell:

PowerShell



The pipeline selects the Runner using:

tags:

&#x20; - windows



10\. GitLab Runner Python PATH Issue

The first Python pipeline failed because the GitLab Runner service could not find Python.

The error was:

python : The term 'python' is not recognized



However, Python worked from the normal user CMD.

The Python installation was:

C:\\Users\\ASPL-PUNE\\AppData\\Local\\Python\\pythoncore-3.14-64



The Python directories were added to the Windows System PATH.

After restarting the GitLab Runner service:

gitlab-runner-windows-amd64.exe restart



Python became available to the Runner environment.

Verification:

python --version



Result:

Python 3.14.6



Runner status:

gitlab-runner-windows-amd64.exe status



Result:

gitlab-runner: Service is running



11\. Why python -m pytest Was Used

Instead of:

pytest



the pipeline uses:

python -m pytest



Similarly, instead of:

pip install



the pipeline uses:

python -m pip install



This avoids problems where the standalone pytest or pip executable is not available in PATH.

12\. GitLab CI Dependency Cache

To improve pipeline efficiency, pip caching was configured.

variables:

&#x20; PIP\_CACHE\_DIR: "$CI\_PROJECT\_DIR/.cache/pip"



cache:

&#x20; paths:

&#x20;   - .cache/pip



The cache allows Python package downloads to be reused between pipeline executions when the cache is available.

Conceptually:

First Pipeline

&#x20;     ↓

Download dependencies

&#x20;     ↓

Store pip cache

&#x20;     ↓

Pipeline completes



Next Pipeline

&#x20;     ↓

Restore cache

&#x20;     ↓

Reuse cached packages

&#x20;     ↓

Faster dependency installation



13\. Cache vs Artifact

GitLab CI cache and artifacts serve different purposes.

Feature	Cache	Artifact

Primary purpose	Speed up future jobs	Preserve job output

Example	pip cache	Python package

Usage	Reuse dependencies	Download/share output

Lifecycle	Temporary/reusable	Pipeline job output





14\. JUnit Test Reporting

The pipeline was enhanced to generate a JUnit-compatible test report.

Pytest command:

\- python -m pytest --junitxml=pytest-report.xml



GitLab configuration:

artifacts:

&#x20; when: always

&#x20; reports:

&#x20;   junit:

&#x20;     - student-api/pytest-report.xml



The flow becomes:

pytest

&#x20;  ↓

pytest-report.xml

&#x20;  ↓

GitLab CI

&#x20;  ↓

JUnit Test Report

&#x20;  ↓

GitLab Test Results



The use of:

when: always



ensures the test report can still be collected when tests fail.

15\. Python Package Building

The project was also prepared for Python packaging using:

pyproject.toml



Configuration:

\[build-system]

requires = \["setuptools>=61"]

build-backend = "setuptools.build\_meta"



\[project]

name = "student-management-api"

version = "1.0.0"

description = "Student Management API"

requires-python = ">=3.14"



The Python build tool was installed using:

python -m pip install build



The package was built using:

python -m build



This generates Python distribution packages in:

dist/



Typical package formats include:

.whl

.tar.gz



A Wheel package is designed for installation, while the source distribution provides the source package.

16\. Python CI/CD Pipeline Flow

The final learning flow is:

Developer

&#x20;   │

&#x20;   ▼

GitLab Repository

&#x20;   │

&#x20;   ▼

GitLab CI Pipeline

&#x20;   │

&#x20;   ▼

Windows GitLab Runner

&#x20;   │

&#x20;   ├── Python version

&#x20;   │

&#x20;   ├── pip version

&#x20;   │

&#x20;   ├── Restore pip cache

&#x20;   │

&#x20;   ├── Install dependencies

&#x20;   │

&#x20;   ├── Run pytest

&#x20;   │

&#x20;   ├── Generate JUnit report

&#x20;   │

&#x20;   └── Build Python package

&#x20;   │

&#x20;   ▼

Pipeline Result



17\. Troubleshooting Performed

Problem 1 — Pytest collected zero tests

Error:

collected 0 items



Investigation showed:

tests/



was empty.

Solution:

Created:

tests/test\_app.py



Problem 2 — pytest command not recognized

Error:

'pytest' is not recognized as an internal or external command



Solution:

Instead of:

pytest



use:

python -m pytest



Problem 3 — Python import error

Error:

ModuleNotFoundError: No module named 'src'



Cause:

pytest was executed from the repository root.

Solution:

cd student-api

python -m pytest



Problem 4 — GitLab Runner could not find Python

Error:

python : The term 'python' is not recognized



Cause:

Python was available to the interactive user environment but not to the Windows GitLab Runner service.

Solution:

1\. Add Python to System PATH.

2\. Restart GitLab Runner.

3\. Verify Python from the Runner environment.

18\. Git Best Practices Practiced

Before committing the Lesson 17 work, the staging area was inspected.

Commands used:

git status



git diff --cached --stat



git diff --cached --name-only



Generated files were excluded using .gitignore.

Examples:

\# Java

student-management/target/



\# Python

student-api/.pytest\_cache/

student-api/\_\_pycache\_\_/

student-api/\*\*/\*.pyc



This prevents build output and Python cache files from being committed.

19\. Production-Oriented Practices Learned

The following practices were applied:

\- Use python -m pip.

\- Use python -m pytest.

\- Keep application source separate from tests.

\- Use requirements.txt for dependencies.

\- Use .gitignore for generated files.

\- Use a self-managed GitLab Runner with tags.

\- Configure dependency caching.

\- Publish JUnit test reports.

\- Generate build artifacts/packages.

\- Inspect Git staging before committing.

\- Troubleshoot the actual CI environment rather than assuming the local environment matches it.

20\. Key Commands

Check Python

python --version



Check pip

python -m pip --version



Install dependencies

python -m pip install -r requirements.txt



Run tests

python -m pytest



Generate JUnit report

python -m pytest --junitxml=pytest-report.xml



Install Python build tool

python -m pip install build



Build package

python -m build



Check Git status

git status



Inspect staged files

git diff --cached --name-only



21\. Final Outcome

Lesson 17 successfully established a working Python CI/CD workflow using GitLab.

The final architecture is:

Python Application

&#x20;      │

&#x20;      ▼

GitLab Repository

&#x20;      │

&#x20;      ▼

GitLab CI/CD

&#x20;      │

&#x20;      ▼

Self-Managed Windows Runner

&#x20;      │

&#x20;      ├── Python

&#x20;      ├── pip

&#x20;      ├── Dependency Cache

&#x20;      ├── pytest

&#x20;      └── JUnit Reports

&#x20;      │

&#x20;      ▼

Python Package



The Python pipeline successfully executed automated tests using the self-managed Windows GitLab Runner.

Test result:

2 passed



22\. Lesson Status

Lesson 17 — Python CI/CD

Status:

COMPLETED



Hands-on:

COMPLETED



GitLab Pipeline:

PASSED



Python Tests:

2 PASSED



Dependency Cache:

CONFIGURED



JUnit Reporting:

CONFIGURED



Python Packaging:

PRACTICED





\### Commit the documentation



After saving the file:



```cmd

cd C:\\Users\\ASPL-PUNE\\gitlab-zero-to-production



Check:

git status



Then:

git add 17-python-cicd\\README.md



Commit:

git commit -m "Document Python CI/CD lesson 17"



Push:

git push origin main



Finally:

git status



You want:

nothing to commit, working tree clean



Lesson 17 is then fully documented and complete.

