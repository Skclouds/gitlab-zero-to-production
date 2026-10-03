Lesson 13 — GitLab CI/CD Variables \& Secrets



\## Overview



This lesson focuses on managing configuration values and sensitive information in GitLab CI/CD pipelines.



The primary objective is to understand how variables can be defined, consumed, protected, masked, scoped to environments, and managed at different levels.



The lesson also demonstrates why sensitive credentials should not be hard-coded inside `.gitlab-ci.yml`.



\---



\# 1. Learning Objectives



By the end of this lesson, the following concepts were covered:



\- Understand GitLab CI/CD variables.

\- Understand project-level CI/CD variables.

\- Understand group-level CI/CD variables.

\- Understand masked variables.

\- Understand protected variables.

\- Understand environment-scoped variables.

\- Understand predefined GitLab CI/CD variables.

\- Understand variable consumption inside CI/CD jobs.

\- Understand variable precedence.

\- Understand the difference between configuration values and secrets.

\- Understand basic secret-management practices.

\- Understand why secrets should not be committed to Git repositories.



\---



\# 2. Why CI/CD Variables Are Required



Real CI/CD pipelines frequently require configuration values and credentials.



Examples include:



```text

Database host

Database username

Database password

API tokens

AWS credentials

JFrog credentials

Docker registry credentials

SonarQube tokens

Application configuration

Environment information



Hard-coding sensitive values directly into .gitlab-ci.yml is not a good practice.



For example:



variables:

&#x20; DB\_PASSWORD: "mypassword123"



This value becomes part of the repository configuration and can potentially remain in Git history.



Instead, sensitive values should be managed using appropriate GitLab CI/CD variable mechanisms.



3\. Basic CI/CD Variable



A variable is a named value that can be reused inside CI/CD jobs.



Example:



variables:

&#x20; APP\_NAME: "student-api"

&#x20; ENVIRONMENT: "development"



The variables can be referenced using:



script:

&#x20; - echo "$APP\_NAME"

&#x20; - echo "$ENVIRONMENT"



Example output:



student-api

development

4\. Project CI/CD Variables



A project CI/CD variable belongs to a specific GitLab project.



Variables can be configured through:



Project

&#x20;  ↓

Settings

&#x20;  ↓

CI/CD

&#x20;  ↓

Variables



Example:



Key:

DEMO\_SECRET



Value:

<secret value>



The value is then available to CI/CD jobs as:



$DEMO\_SECRET



The actual secret does not need to be stored in .gitlab-ci.yml.



5\. Hands-On: Project CI/CD Variable



A project variable named:



DEMO\_SECRET



was created in the GitLab project.



The variable was configured as:



Visibility:

Masked



The value was a dummy practice value and not a real credential.



The purpose was to demonstrate how a variable stored in GitLab can be consumed by a CI/CD job without storing the value in the repository.



6\. Using a Project Variable in a Job



The following job was used for the hands-on exercise:



secret-test:

&#x20; stage: test

&#x20; script:

&#x20;   - echo "Testing GitLab CI/CD variable"

&#x20;   - echo "Secret variable is configured"

&#x20;   - 'echo "Secret value: $DEMO\_SECRET"'



The important point is that the pipeline configuration contains:



$DEMO\_SECRET



but does not contain the actual secret value.



The value is supplied by GitLab when the job executes.



7\. GitLab Runner and CI/CD Variables



The execution flow is:



GitLab Project

&#x20;     │

&#x20;     ▼

CI/CD Variable

&#x20;     │

&#x20;     ▼

Pipeline

&#x20;     │

&#x20;     ▼

GitLab Runner

&#x20;     │

&#x20;     ▼

CI Job

&#x20;     │

&#x20;     ▼

$DEMO\_SECRET



The GitLab Runner executes the job and receives the variable as part of the CI/CD job environment.



8\. Masked Variables



A masked variable is configured so that GitLab attempts to prevent its value from appearing directly in job logs.



Example:



Secret value: \[MASKED]



The purpose of masking is to reduce accidental exposure of sensitive values through CI/CD logs.



However:



Masking should not be treated as a guarantee that a secret can never be exposed.



A CI/CD job can still access the variable.



Therefore, production pipelines should avoid printing secrets to logs even when masking is enabled.



9\. Masked and Hidden Variables



GitLab also provides stronger visibility controls depending on the GitLab version and variable configuration options.



Conceptually:



Masked

&#x20;   ↓

Attempts to hide the value in job logs



Masked and hidden

&#x20;   ↓

Attempts to hide the value in job logs

\+

Limits normal UI exposure of the value



For sensitive credentials, an appropriate hidden/masked configuration should be considered according to the organization's security requirements.



10\. Protected Variables



A protected variable is associated with protected branches or tags.



Example:



Protect variable:

Enabled



The purpose is to prevent sensitive variables, such as production credentials, from being made available to pipelines running on unprotected branches.



Example:



feature/\*

&#x20;   ↓

No production secret



develop

&#x20;   ↓

No production secret



main (protected)

&#x20;   ↓

Production secret available

11\. Masked vs Protected



These concepts solve different problems.



Feature	Purpose

Visible	Variable value can potentially appear in logs

Masked	Attempts to hide value in job logs

Masked and hidden	Provides stronger protection of the value's visibility

Protected	Controls availability based on protected branches/tags



A variable can be configured as both:



Masked

\+

Protected



This is a common pattern for sensitive production credentials.



12\. Why Protected Variables Are Important



Consider a production API token:



PRODUCTION\_API\_TOKEN



A feature branch should normally not have access to this credential.



A safer model is:



Feature Branch

&#x20;     │

&#x20;     ├── Build

&#x20;     ├── Test

&#x20;     └── Quality Checks

&#x20;     │

&#x20;     └── No production credentials



Protected Main

&#x20;     │

&#x20;     ├── Build

&#x20;     ├── Test

&#x20;     └── Production Deployment

&#x20;             │

&#x20;             └── Production credentials



This follows the principle of limiting sensitive credentials to the pipelines that require them.



13\. Environment-Scoped Variables



Different environments often require different configuration values.



Typical environments include:



development

staging

production



For example:



development → dev database

staging     → staging database

production  → production database



An environment-scoped variable allows the same variable name to have different values for different environments.



Example:



DATABASE\_HOST



could conceptually have:



development → dev-database.example.com

staging     → staging-database.example.com

production  → prod-database.example.com

14\. GitLab Environment



A CI/CD job can be associated with an environment using:



deploy-development:

&#x20; stage: deploy

&#x20; environment:

&#x20;   name: development

&#x20; script:

&#x20;   - echo "Deploying to development"



The important configuration is:



environment:

&#x20; name: development



This tells GitLab that the job is associated with the development environment.



15\. Environment-Specific Variable Hands-On



A practice variable was created:



Key:

DATABASE\_HOST



with a development environment scope.



Example practice value:



dev-database.example.com



The value was intentionally a dummy value and not a real production database address.



A deployment job was then configured:



deploy-development:

&#x20; stage: deploy

&#x20; environment:

&#x20;   name: development

&#x20; script:

&#x20;   - echo "Starting development deployment"

&#x20;   - 'echo "Database host: $DATABASE\_HOST"'

&#x20;   - echo "Development deployment completed"



The pipeline can therefore associate:



deploy-development

&#x20;       ↓

development environment

&#x20;       ↓

DATABASE\_HOST

&#x20;       ↓

dev-database.example.com

16\. Group CI/CD Variables



Project variables belong to individual projects.



Group variables can be managed at the GitLab group level and can be inherited by projects within the appropriate group hierarchy.



Example:



GitLab Learning

&#x20;      │

&#x20;      ▼

Group CI/CD Variables

&#x20;      │

&#x20;      ├── SONAR\_HOST\_URL

&#x20;      ├── JFROG\_URL

&#x20;      └── DOCKER\_REGISTRY

&#x20;      │

&#x20;      ▼

Projects within the group



This is useful when multiple projects share common configuration.



17\. Project vs Group Variables

Variable Type	Scope

Project variable	Individual project

Group variable	Group and applicable child projects



Example:



Project:

APP\_NAME



Group:

SONAR\_HOST\_URL

JFROG\_URL

DOCKER\_REGISTRY



Project-specific configuration can remain at the project level while common organization-wide configuration can be managed at the group level.



18\. GitLab Learning Group



A GitLab group named:



GitLab Learning



was created during the GitLab learning exercises.



The group can be used to demonstrate group-level CI/CD variables.



Example:



Key:

COMMON\_TOOL\_NAME



Value:

GitLab-DevOps-Tools



The intended architecture is:



GitLab Learning

&#x20;      │

&#x20;      ▼

Group CI/CD Variable

&#x20;      │

&#x20;      ▼

Projects within the group

&#x20;      │

&#x20;      ▼

CI/CD Pipeline

&#x20;      │

&#x20;      ▼

$COMMON\_TOOL\_NAME



A project must be within the group's hierarchy for group-variable inheritance to apply.



19\. Variable Precedence



Variable precedence determines which value is used when the same variable is defined in multiple locations.



For example:



Group:

APP\_NAME = group-app



Project:

APP\_NAME = project-app



Job:

APP\_NAME = job-app



A more specific definition can override a broader definition, subject to GitLab's complete variable precedence rules.



This is important when troubleshooting unexpected values in CI/CD pipelines.



20\. Hands-On Variable Precedence



A practice variable can be created at the project level:



Key:

PRECEDENCE\_TEST



Value:

project-value



A job can then define the same variable:



precedence-test:

&#x20; stage: test



&#x20; variables:

&#x20;   PRECEDENCE\_TEST: "job-value"



&#x20; script:

&#x20;   - 'echo "PRECEDENCE\_TEST = $PRECEDENCE\_TEST"'



The job-level value demonstrates that a more specific definition can override the project-level value.



Expected output:



PRECEDENCE\_TEST = job-value

21\. Important Security Rules



The following practices should be followed when working with CI/CD secrets.



Do not hard-code credentials



Avoid:



variables:

&#x20; DB\_PASSWORD: "real-password"

Do not commit API tokens



Avoid storing:



API\_TOKEN

AWS\_SECRET\_KEY

JFROG\_TOKEN

DATABASE\_PASSWORD



directly in source code.



Use GitLab CI/CD Variables



Sensitive values should be managed through appropriate GitLab variable mechanisms.



Use masking where appropriate



Sensitive values should generally be configured so that accidental exposure in logs is reduced.



Use protection for production credentials



Production credentials should generally be restricted to the pipelines that actually require them.



Do not print secrets



Avoid:



script:

&#x20; - echo "$DB\_PASSWORD"



even if the variable is masked.



Instead, use the variable directly in the command that needs it.



22\. Production CI/CD Secret Architecture



A production-style setup can look like:



&#x20;                        GitLab

&#x20;                          │

&#x20;             ┌────────────┴────────────┐

&#x20;             │                         │

&#x20;        Group Variables          Project Variables

&#x20;             │                         │

&#x20;             ▼                         ▼

&#x20;      Common configuration       Project configuration

&#x20;             │                         │

&#x20;             └────────────┬────────────┘

&#x20;                          │

&#x20;                          ▼

&#x20;                   CI/CD Pipeline

&#x20;                          │

&#x20;                          ▼

&#x20;                    GitLab Runner

&#x20;                          │

&#x20;                          ▼

&#x20;                       Job

&#x20;                          │

&#x20;               ┌──────────┴──────────┐

&#x20;               ▼                     ▼

&#x20;         Configuration            Secrets



Production secrets should be managed with appropriate scope and access restrictions.



23\. Common Mistakes

Mistake 1 — Hard-coding secrets

DB\_PASSWORD: "password123"



Avoid this.



Mistake 2 — Printing secrets

echo "$DB\_PASSWORD"



Avoid exposing credentials through logs.



Mistake 3 — Giving production credentials to feature branches



Production secrets should not normally be available to arbitrary unprotected branches.



Mistake 4 — Confusing masked and protected



Masked:



Controls exposure of the value



Protected:



Controls availability based on protected refs



They are different security controls.



Mistake 5 — Unexpected variable overrides



If the same variable exists at multiple scopes, its effective value may not be the one expected.



Always consider variable precedence when troubleshooting.



24\. Hands-On Summary



The following concepts were practiced:



✓ Project CI/CD Variables

✓ Masked Variables

✓ Protected Variables

✓ Environment-Scoped Variables

✓ Group CI/CD Variables

✓ Variable Consumption in Jobs

✓ Variable Precedence

✓ Secret Management Concepts

✓ GitLab Runner Variable Usage

25\. Key Takeaways

Project Variable



Used for project-specific configuration or secrets.



Project

&#x20;  ↓

CI/CD Variable

Group Variable



Used for common configuration across projects in a group hierarchy.



Group

&#x20;  ↓

Projects

Masked



Helps prevent sensitive values from appearing plainly in logs.



Protected



Restricts variable availability to protected branches/tags according to GitLab's configuration.



Environment Scope



Allows different values for environments such as:



development

staging

production

Variable Precedence



Determines which value is used when the same variable is defined at multiple scopes.



26\. Lesson 13 Completion Checklist

&#x20;Understand CI/CD variables

&#x20;Create a project CI/CD variable

&#x20;Use a project variable in a pipeline

&#x20;Understand masked variables

&#x20;Understand masked and hidden variables

&#x20;Understand protected variables

&#x20;Understand masked vs protected

&#x20;Understand environment-scoped variables

&#x20;Associate jobs with environments

&#x20;Understand group CI/CD variables

&#x20;Understand project vs group variables

&#x20;Understand variable precedence

&#x20;Understand secret-management fundamentals

&#x20;Understand why secrets should not be hard-coded

&#x20;Understand why secrets should not be printed in logs

