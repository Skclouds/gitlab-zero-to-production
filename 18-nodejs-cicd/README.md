Lesson 18 — Node.js CI/CD with GitLab



\## 1. Overview



This lesson focuses on building a complete Node.js CI/CD pipeline using GitLab CI/CD.



The objective is to understand how a Node.js application can be automatically tested and built whenever changes are pushed to GitLab.



\### Pipeline Flow



Developer → Git Push → GitLab CI/CD → Test → Build → Artifact



The hands-on project created in this lesson is:



`Student Management Node API`



\---



\# 2. Learning Objectives



By completing this lesson, I learned how to:



\- Create a Node.js project

\- Initialize a project using npm

\- Understand `package.json`

\- Understand `package-lock.json`

\- Install development dependencies

\- Configure Jest

\- Write Node.js unit tests

\- Run tests using npm

\- Use `npm ci` in CI/CD

\- Configure `.gitignore`

\- Configure NPM dependency caching

\- Configure GitLab CI/CD for Node.js

\- Use GitLab Runner for Node.js jobs

\- Create a Node.js build process

\- Generate a `dist` directory

\- Store build output as a GitLab artifact

\- Create a Test → Build pipeline

\- Understand the relationship between application code and CI/CD



\---



\# 3. Technologies Used



| Technology | Purpose |

|---|---|

| Node.js | JavaScript runtime |

| npm | Package and dependency management |

| Jest | Unit testing |

| Git | Version control |

| GitLab | Source code and CI/CD platform |

| GitLab Runner | Executes CI/CD jobs |

| YAML | Pipeline configuration |



\---



\# 4. Project Structure



The Node.js project was created inside the GitLab hands-on repository.



```text

student-node-api/

│

├── .gitignore

├── package.json

├── package-lock.json

│

├── src/

│   └── app.js

│

└── tests/

&#x20;   └── app.test.js



After the build:

student-node-api/

│

├── .gitignore

├── package.json

├── package-lock.json

│

├── src/

│   └── app.js

│

├── tests/

│   └── app.test.js

│

└── dist/

&#x20;   └── app.js



5\. Creating the Node.js Project

The project was created using:

mkdir student-node-api

cd student-node-api

npm init -y



This created the initial package.json.

6\. package.json

package.json contains important information about the Node.js application.

It can define:

\- Project name

\- Version

\- Description

\- Scripts

\- Dependencies

\- Development dependencies

\- Project configuration

The project used the following scripts:

"scripts": {

&#x20; "test": "jest",

&#x20; "build": "if not exist dist mkdir dist \&\& xcopy /E /I /Y src dist"

}



The scripts can be executed using:

npm test



and:

npm run build



7\. package-lock.json

package-lock.json records the dependency tree and exact dependency information installed for the project.

It helps provide consistent dependency installation across environments.

For CI/CD, this is particularly important because the pipeline should install the same dependency versions used by the project.

8\. Installing Jest

Jest was installed as a development dependency:

npm install --save-dev jest



The installed Jest version was verified using:

npx jest --version



Jest was used for automated unit testing.

9\. Application Code

The application was created in:

src/app.js



Code:

function getApplicationName() {

&#x20;   return "Student Management Node API";

}



function getStudentCount() {

&#x20;   return 10;

}



module.exports = {

&#x20;   getApplicationName,

&#x20;   getStudentCount

};



The application contains two functions:

getApplicationName()

Returns:

Student Management Node API



getStudentCount()

Returns:

10



10\. Unit Tests

Tests were created in:

tests/app.test.js



Code:

const {

&#x20;   getApplicationName,

&#x20;   getStudentCount

} = require("../src/app");



test("application name should be correct", () => {

&#x20;   expect(getApplicationName()).toBe("Student Management Node API");

});



test("student count should be correct", () => {

&#x20;   expect(getStudentCount()).toBe(10);

});



The tests verify:

1\. Application name

2\. Student count

11\. Running Unit Tests

Tests were executed using:

npm test



The test result was:

PASS tests/app.test.js



Test Suites: 1 passed, 1 total

Tests:       2 passed, 2 total



This confirmed that the application passed the local unit tests.

12\. .gitignore

A .gitignore file was created:

node\_modules/

coverage/

dist/



The purpose is to prevent generated or unnecessary files from being committed to Git.

node\_modules

Contains installed Node.js dependencies.

It should generally not be committed because dependencies can be installed using:

npm ci



coverage

Contains generated test coverage information.

dist

Contains generated build output.

13\. npm install vs npm ci

A key CI/CD concept is the difference between:

npm install



and:

npm ci



npm install

Primarily used during development when installing or updating dependencies.

npm ci

Designed for clean and reproducible CI environments.

It uses:

package-lock.json



to install the locked dependency versions.

For CI/CD pipelines, npm ci is preferred when a lock file is available.

14\. GitLab Runner

The Node.js pipeline was executed using the self-managed Windows GitLab Runner created earlier.

The pipeline jobs used the Runner tag:

tags:

&#x20; - windows



This tells GitLab to select the Runner having the windows tag.

15\. Initial Node.js CI Pipeline

The initial Node.js pipeline contained a test stage:

stages:

&#x20; - test



node-tests:

&#x20; stage: test

&#x20; tags:

&#x20;   - windows

&#x20; script:

&#x20;   - cd student-node-api

&#x20;   - node --version

&#x20;   - npm --version

&#x20;   - npm ci

&#x20;   - npm test



The pipeline performed:

Node version

&#x20;    ↓

npm version

&#x20;    ↓

npm ci

&#x20;    ↓

npm test



The Node.js test job successfully executed on the GitLab Runner.

16\. NPM Dependency Cache

Dependency caching was introduced to improve pipeline efficiency.

Configuration:

variables:

&#x20; NPM\_CONFIG\_CACHE: "$CI\_PROJECT\_DIR/student-node-api/.npm"



cache:

&#x20; paths:

&#x20;   - student-node-api/.npm/



The cache stores the NPM cache directory between pipeline runs.

This can reduce the amount of dependency data that needs to be downloaded repeatedly.

17\. Node.js Build

A build script was added to package.json:

"build": "if not exist dist mkdir dist \&\& xcopy /E /I /Y src dist"



The build was executed with:

npm run build



The build successfully generated:

dist/

└── app.js



The generated file was verified using:

dir dist



and:

type dist\\app.js



The resulting dist/app.js contained the application code.

18\. Complete GitLab CI/CD Pipeline

The final pipeline contains two stages:

test

&#x20; ↓

build



Pipeline configuration:

stages:

&#x20; - test

&#x20; - build



variables:

&#x20; NPM\_CONFIG\_CACHE: "$CI\_PROJECT\_DIR/student-node-api/.npm"



cache:

&#x20; paths:

&#x20;   - student-node-api/.npm/



node-tests:

&#x20; stage: test

&#x20; tags:

&#x20;   - windows

&#x20; script:

&#x20;   - cd student-node-api

&#x20;   - node --version

&#x20;   - npm --version

&#x20;   - npm ci

&#x20;   - npm test



node-build:

&#x20; stage: build

&#x20; tags:

&#x20;   - windows

&#x20; script:

&#x20;   - cd student-node-api

&#x20;   - npm ci

&#x20;   - npm run build

&#x20; artifacts:

&#x20;   paths:

&#x20;     - student-node-api/dist/

&#x20;   expire\_in: 1 week



19\. Pipeline Execution Flow

The complete pipeline works as follows:

&#x20;                   Git Push

&#x20;                      │

&#x20;                      ▼

&#x20;             GitLab CI/CD Pipeline

&#x20;                      │

&#x20;                      ▼

&#x20;             ┌─────────────────┐

&#x20;             │   Test Stage    │

&#x20;             │                 │

&#x20;             │ npm ci          │

&#x20;             │ npm test        │

&#x20;             └────────┬────────┘

&#x20;                      │

&#x20;                   SUCCESS

&#x20;                      │

&#x20;                      ▼

&#x20;             ┌─────────────────┐

&#x20;             │   Build Stage   │

&#x20;             │                 │

&#x20;             │ npm ci          │

&#x20;             │ npm run build   │

&#x20;             └────────┬────────┘

&#x20;                      │

&#x20;                   SUCCESS

&#x20;                      │

&#x20;                      ▼

&#x20;                dist/app.js

&#x20;                      │

&#x20;                      ▼

&#x20;             GitLab Artifact



The build stage runs only after the test stage succeeds.

This creates an important CI/CD principle:

Build should not proceed when the required tests have failed.



20\. GitLab Artifacts

The build output is stored using GitLab artifacts:

artifacts:

&#x20; paths:

&#x20;   - student-node-api/dist/

&#x20; expire\_in: 1 week



The artifact contains:

student-node-api/dist/

└── app.js



Artifacts allow generated files from a CI job to be retained and downloaded from GitLab.

21\. Why Artifacts Are Important

Artifacts are useful when a pipeline generates something that must be consumed later.

Examples include:

\- Compiled applications

\- JAR files

\- Node.js build output

\- Test reports

\- Coverage reports

\- Deployment packages

A typical production pipeline may follow:

Source Code

&#x20;    ↓

Test

&#x20;    ↓

Build

&#x20;    ↓

Artifact

&#x20;    ↓

Security/Quality Checks

&#x20;    ↓

Deployment



22\. Hands-on Commands

Create project

mkdir student-node-api

cd student-node-api

npm init -y



Install Jest

npm install --save-dev jest



Check Jest

npx jest --version



Run tests

npm test



Install dependencies using lock file

npm ci



Run build

npm run build



Check build output

dir dist



Display generated file

type dist\\app.js



23\. Git Commands Used

The Lesson 18 work was performed on:

feature/lesson18-nodejs-cicd



Typical commands:

git status



git add .gitlab-ci.yml student-node-api\\package.json



git commit -m "Add Node.js CI test and build pipeline"



git push origin feature/lesson18-nodejs-cicd



24\. Important Concepts Learned

Node.js

Runtime used to execute JavaScript outside the browser.

npm

Package manager used to install dependencies and execute project scripts.

Jest

Testing framework used to create and execute automated unit tests.

npm ci

Used for clean and reproducible dependency installation in CI environments.

GitLab Runner

Executes the commands defined inside GitLab CI jobs.

Cache

Used to improve pipeline performance by reusing dependency data.

Build

Transforms or prepares application source code into build output.

Artifact

A file or directory generated by a CI job and retained by GitLab.

25\. Production CI/CD Best Practices

1\. Commit package-lock.json

The lock file should be version controlled.

2\. Use npm ci in CI

Prefer:

npm ci



instead of:

npm install



for reproducible CI installations.

3\. Do not commit node\_modules

Use:

node\_modules/



in .gitignore.

4\. Run tests before build

The pipeline should validate the application before generating build output.

5\. Cache dependencies

Caching can reduce repeated dependency downloads.

6\. Store important build output as artifacts

Use GitLab artifacts when another pipeline stage or person needs the generated output.

7\. Use Runner tags

Tags ensure jobs are executed by an appropriate Runner.

8\. Keep CI configuration readable

Use clear job names and stages.

26\. Troubleshooting

Problem: npm build script missing

Error:

npm error Missing script: "build"



Cause

The build script had not yet been added to package.json.

Solution

Add:

"build": "if not exist dist mkdir dist \&\& xcopy /E /I /Y src dist"



Then verify:

npm run



The build script should appear under:

available via `npm run`:

&#x20; build



Then run:

npm run build



27\. Build Verification

After running:

npm run build



the following directory was generated:

dist/

└── app.js



The generated file was checked using:

type dist\\app.js



The output contained:

function getApplicationName() {

&#x20;   return "Student Management Node API";

}



function getStudentCount() {

&#x20;   return 10;

}



This confirmed that the build copied the application source into the build directory.

28\. Final Architecture

The Lesson 18 architecture can be represented as:

Developer

&#x20;   │

&#x20;   │ git push

&#x20;   ▼

GitLab Repository

&#x20;   │

&#x20;   ▼

.gitlab-ci.yml

&#x20;   │

&#x20;   ▼

GitLab CI/CD

&#x20;   │

&#x20;   ├───────────────┐

&#x20;   ▼               ▼

&#x20;Test Job        Build Job

&#x20;   │               │

&#x20;npm ci          npm ci

&#x20;npm test        npm run build

&#x20;   │               │

&#x20;   ▼               ▼

Tests Pass       dist/

&#x20;                   │

&#x20;                   ▼

&#x20;             GitLab Artifact



29\. Lesson 18 Outcome

By completing this lesson, I built a working Node.js CI/CD pipeline using GitLab.

The pipeline can:

Install Dependencies

&#x20;       ↓

Run Unit Tests

&#x20;       ↓

Build Application

&#x20;       ↓

Generate dist/

&#x20;       ↓

Store Build Artifact



This provides the foundation for more advanced Node.js pipelines involving:

\- Code quality

\- Security scanning

\- Docker

\- Container Registry

\- Deployment

\- Environments

\- Kubernetes

30\. Key Takeaways

Node.js

&#x20;  ↓

npm

&#x20;  ↓

Jest

&#x20;  ↓

Unit Testing

&#x20;  ↓

GitLab CI/CD

&#x20;  ↓

GitLab Runner

&#x20;  ↓

npm ci

&#x20;  ↓

Build

&#x20;  ↓

dist/

&#x20;  ↓

GitLab Artifact



Lesson 18 completed: Node.js CI/CD with GitLab.

