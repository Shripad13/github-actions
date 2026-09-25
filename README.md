## Github Actions-
1. CI Framework on the top of github platform.
2. your repository should be in github.
3. Only free for individual users & public repositories.
4. For enterprise its paid based on usage.
5. Action platform managed by github itself.
6. You are not responsible for managing & maintaining the underlying platform.
7. you can create runners (worker nodes) on your own infra or use github hosted runners.
8. To use github actions, you repositories should be under github organizations.
9. To use github actions, you need to create workflow files in .github/workflows/blank.yml directory in your repository.
10. workflows nothing but a pipeline.



Github Branch ---> Compile Code ---> SCA Code Quality ---> Security Checks of Code --> Unit Testing ---> Integration Testing ---> Build Artifacts (Binaries) ---> Version the Package ---> Store Binaries in Nexus/JFrog Artifactory (ECR) ---> Deploy to Dev/QA/Prod Environments.


Static Code Analysis Tools (SCA) - SonarQube, Checkmarx, Veracode, Snyk, WhiteSource, Fortify

> If you want to do Manual approval for any stage then you can add Environement in Settings & update Approval.
> Also you keep Wait time if after approval also job should wait before triggering.



##
Workflows are our pipelines
Workflows - Automation process defined in .github/workflows/*.yml
Job - Group of steps running on the same runner.
Step - A step can be an action or a script to be executed/ Individual task inside a job.
Action - An action is a reusable extension that can be used in a step
Runners - will be maintained by you.

# Basic Syntax of Workflow file -
name: CI
on: push
jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
      - run: npm install
      - run: npm test

# Github Action Runner Enrollment Steps -
1. We can enroll the runner at repo level that means only workflows under that repo can use that self-hosted runner.
2. We can also enroll the runner at organization level that means all the repos under that organization can use that self-hosted runner.
3. Enterprise-Level runners can be assigned to multiple organizations in an enterprise account.
   
4. Go to your repo---> Settings ---> Actions ---> Runners ---> New self-hosted runner
5. Select the OS of your runner (Linux/ Windows/ Mac)
6. Follow the steps mentioned there to install the runner on your infra.
7. Once the runner is installed, it will be in idle state waiting for the jobs to be assigned.
8. Once the workflow is triggered, the jobs will be picked by the self-hosted runner & the steps will be executed.

> If self-hosted runner is not picking the jobs, then check the runner group & allow for public repositories if your repo is public.

for nodejs application - we compile the code using npm install & npm build commands.
for java application - we compile the code using maven/ gradle commands.
for python application - we compile the code using pip commands.    
for go application - we compile the code using go build commands.


# How Unit Testing-
$ npm test - command is used for unit testing in package.json file of nodejs application.
$ npm verify - command used for integration testing in package.json file of nodejs application.

# Real Enterprise Flow

Feature Branch
   ↓
Pull Request
   ↓
develop → DEV deploy
   ↓
qa → QA deploy
   ↓
staging → STAGING deploy
   ↓
main → PROD deploy

# Most Maintainable Pattern
Single reusable deployment workflow
+
Environment secrets
+
Branch-based triggers
+
Approval gates for production
+
Infra as code

# Recommended Folder Structure
.github/
└── workflows/
    ├── ci.yml
    ├── deploy.yml
    └── reusable-deploy.yml



# Environment Secrets (Highly Recommended)
Store different secrets:
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
DB_URL
API_URL

Usage like below -
 env:
  API_URL: ${{ secrets.API_URL }}
  DB_URL: ${{ secrets.DB_URL }}

# If deploying to multiple regions/services:
strategy:
  matrix:
    region: [us-east-1, ap-south-1]  


# Use Concurrency
Avoid overlapping deployments:
concurrency:
  group: deploy-${{ github.ref }}
  cancel-in-progress: true


> github.sha is a built-in context variable in GitHub Actions that represents the commit SHA (hash) of the code that triggered the workflow.

# github.sha   comes from
It depends on what triggered the workflow:
Push event → the commit that was pushed
Pull request event → the merge commit or PR head commit (depending on config)
Workflow dispatch → the commit you selected or latest branch commit

| ------------------- | --------------------------------- |
| Variable            | Meaning                           |
| ------------------- | --------------------------------- |
| `github.sha`        | Full commit hash                  |
| `github.ref`        | Full branch or tag ref            |
| `github.ref_name`   | Clean branch name (`main`, `dev`) |
| `github.actor`      | User who triggered workflow       |
| `github.repository` | repo name                         |
| ------------------- | --------------------------------- |

# How to use github.sha in workflow?
- name: Build and Tag Docker Image
  run: |
    docker build -t myapp:${{ github.sha }} .
    docker tag myapp:${{ github.sha }} myrepo/myapp:${{ github.sha }}
    docker push myrepo/myapp:${{ github.sha }}

# What is the difference between a fast forward merge & a 3-way merge?
Fast forward merge:
- Occurs when the branch being merged has not diverged from the target branch.
- Simply moves the target branch pointer forward to the latest commit of the source branch.
- No new commit is created for the merge.
3-way merge:
- Happens when the source branch has diverged from the target branch (i.e., both branches have new commits).
- Git creates a new merge commit that combines the changes from both branches.
- Requires a common ancestor commit to perform the merge.

# Can you explain what git blame does and when you might use it?
`git blame` is a command that shows the last modification for each line of a file, including the author and the commit hash. It is used to identify who made changes to specific lines of code and when those changes were made. This can be helpful for debugging, understanding the history of a file, or finding out why a particular change was made.




# STATIC CODE ANALYSIS TOOLS (SCA) - 
'''
 In Static Code Analysis SONARQUBE is the Leader

1. Analysing the code which pushed to the repo
2. Determines the code quality
3. what is % of test cases that are covered
4. How many security vulnerabilities/ hotspots are there in the code.
5. How many secrets/passwords/tokens are published to the repo by mistake.
6. How many duplications are there in the code. (In % )
7. Standardization of code as per the industry best practices.
8. Based on the SCA report, we can take actions to improve the code quality & fix the security vulnerabilities.

'''
 Who is going to determine the whether vulnerabilities in the package or not? 
 Who is going to determine the whether issues in the package or not?
 Thats where SAST tools comes into picture.

In Security Check or Testing:
# SAST - Static Application Security Testing - gives insights & inputs about the overall code vulnerabilities & issues, dependencies going to download , security problems in the code.

1. SAST gives Overall Security posture of the application code.
2. Including packages that are going to be downloaded as part of dependencies from the internet.

 In SAST CheckMarx is Leader - So Costly Tool

# Software Composition Analysis (SCA) - gives insights about the open source libraries vulnerabilities that are being used in the application.
1. Check Open Source/ 3rd party libraries vulnerabilities that are being used in the application.
2. Check whether any license violations are there in the open source libraries that are being used in the application.
3. Check whether any known vulnerabilities are there in the open source libraries that are being used in the application.
 In SCA - Whitesource is Leader - Costly Tool
 CHeckMarx SCA module is also good.


 # DAST - Dynamic Application Security Testing 
 1. It is performed on running application.
 2. After the deployment, Using the endpoint of your application does the Whitebox Testing.
   

```
Static Code Analysis - Pushed to the repo & check as per org standards or not (SonarQube tool)
SAST                 - Overall Security Posture of code (CheckMarx) & industry std
Software Composition Analysis - Open Source libraries Vulnerability (CheckMarx SCA) & licensing issues or not?
DAST - endpoint of application can be tested (Whitebox testing (OWASP ZAP, Acunetix)) (Post Deployment)
```


# Sonarqube Report Details- 
1. Quality Gate - Pass/ Fail status of the code based on the quality metrics defined.
2. Bugs & Vulnerabilities - Number of bugs & vulnerabilities found in the code.
3. Code Smells - Maintainability issues in the code.
4. Duplications - Percentage of duplicated code & blocks
5. Coverage - Percentage of code covered by unit tests.
6. Lines of Code - Total number of lines of code analyzed.
7. Security Hotspots - Potential security issues that need to be reviewed.
8. Technical Debt - Estimated time to fix all maintainability issues.
9. New Bugs & Vulnerabilities, Debt, Code Smells - Issues introduced in the latest code changes.

Code smells - they indicate poor software design patterns—such as dead code, massive classes, or duplicated logic—that lead to long-term technical debt 

We can maintain Sonarqube on our server
We can go with Software as Service

# What is Quality Gate? Who will determine its passed or failed?

> Important terms on SonarQube
 Quality Gate
 Quality Profile

Update the Quality Gate as per our requirement & standards for Passed & Failed.


# SonarQube Integration with GitHub Actions CI/CD Pipeline (Static Code Analysis) -
1. On EC2 instance we can installed & maintain Sonarqube server
2. We will Install SONAR scanner on CI-Runner instance
2. Sonar scanner scan the code & push the report to Sonarqube server.
/workspace/repo --> sonar scanner scans the code --> pushes the report to Sonarqube server.
3. For Enterprise Level we need to Pay, Community Edition is Free.
4. License will be based on number of lines of code will be analyzed.
5. Bydefault Sonarqube port is 9000
6. Requires Big EC2 instance for Sonarqube server, in terms of CPU & RAM.
7. SOnarqube requires JAVA Version 17 or above version to run the server.


# Static Code Analysis -
1. Provision a new server for sonarqube along with a DNS record.
2. Configure Sonarqube on this instance using ansible.
3. On the top of the ci-runner, I would like to install sonar scanner using ansible.
4. In the github actions workflow file, after the code compilation step, I would like to add a new step to run sonar scanner command to scan the code & push the report to sonarqube server.
5. Sonar scanner command requires following inputs -
   a. SonarQube server URL
   b. SonarQube authentication token
   c. Project key (unique identifier for the project in SonarQube)
   d. Project name
   e. Source code directory path

# problem Statement -
We dont want the token to be copy pasted from Github Actions from UI all the time to code.
I want this to be fetched all the time dynamically from github secrets.
Github offers, "gh cli" using from the ci-runner connect with github, get the token & run.

Solution - Use this gh API to fetch runner token dynamically from github secrets.
```
$ gh api --method POST -H "Accept: application/vnd.github+json" /repos/Shripad13/expense-backend/actions/runners/registration-token
```


# How to register Gitlab runner & token using gh cli on CI-Runner?

1. Installl Github CLI on the CI-Runner using ansible.
$ curl https://raw.githubusercontent.com/Shripad13/github-actions/refs/heads/main/gh-cli.sh | sudo bash

2. Authenticate to Github
$ gh auth login

3. Generate the CLI token using below command
$ gh api --method POST -H "Accept: application/vnd.github+json" /repos/Shripad13/expense-backend/actions/runners/registration-token |jq .token |xargs 
$ gh api --method POST -H "Accept: application/vnd.github+json" /org/Shripad13/actions/runners/registration-token |jq .token |xargs


# Next Steps -
1. Sonar scanner to be installed on the CI-Runner using ansible.
2. This ci-runner should scan the code & send the analysis report to sonarqube server.
3. Then based on the result we will make a decision, whether to proceed with the deployment or not.
4. If Sonarqube mentioned there are any vulnerabilities/ bugs/ code smells, we will fail the pipeline & notify the developers to fix the issues.

# How to supply sonarqube URL to sonar scanner for pushing the report?
Command for scanner -
 $ sudo sonar-scanner/sonar-scanner-6.2.1.4610-Linux-x64/bin/sonar-scanner -Dsonar.host.url=http://<Private IP>:9000 -Dsonar.projectKey=expense-backend -Dsonar.login=admin -Dsonar.password=<sonarqubepwd>

# How ci runner can authenticate to vault & get the sonarqube password?
Sonar password has to be stored on vault.
ci-runner should authenticate to vault to get the Sonarqube password 
before that, ci-runner needs a vault token, This vault token, will keep it on Github Password Manger, using this token ci-runner wll authenticates to vault & get the Sonarqube password.

# Container scanning - 
Container scanning tools analyze container images (like Docker) for vulnerabilities, misconfigurations, and secrets, integrating into CI/CD pipelines to find issues early, with popular open-source options like Trivy, Grype, and Clair, and enterprise platforms including Snyk, Wiz, Aqua Security, and Prisma Cloud, helping developers shift security left for more secure deployments. 

Because we will have Base image in Docker image (node image/alpine image) that should not contain vulnerabilities & issues

1. Push the image to ECR.
2. Change scan setting to Manual on ECR. (IMP Step)
3. Trigger ECR scan Manually.
4. Wait for the scan to complete.
5. Next get the findings of this image from ECR.
6. Proceed only if the Vulnerabilities are zero.

In Organization usually, When Critical & High vulenrabilities is zero in ECR for Image scanning then only it will proceed for next.


# Command to start-image-scan
```
aws ecr start-image-scan --repository-name sample-repo --image-id imageTag=${e885e6}

```

0. Kubectl and Arcgo CLI should be installed on ci-runner
1. EKS Dev deployment should be done by ci-runner using ArgoCD
 
 >> Tunning
 1. We are not really deploying the image went through CI. 
 2. We need to update our helm-charts accordingly.
 3. gitsha should be supplied dynamically from the workflow.
 4. argocd app create should only happen if the app is not available.
 5. If the app is already available, if the imagetag is different, it should also do the deployment with the new image.
   

> Production Deployment (RFC)
1. Before you Deploy anything on prod, it has to be presented & qualified in the CAB meeting.
2. Is it tested on the lower env or not?
3. When it was deployment in lower env (might have n days of cooling period)
4. What is rollback strategy?
5. Is it going ot cause any downtime?


# Implement 4 golden signals for expense app on EKS
1. Prometheus & grafana on Standalone instance, we dont need them anymore
2. We can deploy prometheus & grafana on the same EKS Cluster

#################################################################################


## After CI CD, need to deploy APp on EKS instead of VM
1. Would like to deploy prometheus & grafana on the EKS
2. Deploy filebeat to extract the logs from the deployments & send to ELK
3. Then Project the metrics on ELK



### In DevSecOps, the Shift Left Model means:
Shift Left is the practice of integrating security and quality checks as early as possible in the SDLC to prevent issues before they reach production.
The goal is to prevent vulnerabilities instead of detecting them in production.

Think of the SDLC timeline as moving left → right
Idea → Code → Build → Test → Deploy → Operate
🔁 Shift Left = catch problems as early as possible

Why “Shift Left” Matters/important?
Fixing issues earlier is:
💸 Cheaper (bugs found in prod cost much more)
⚡ Faster (no emergency patches)
🔐 Safer (vulnerabilities caught before release)
📉 Lower risk (less blast radius)

🔁 Shift Right (Old Way)
Vulnerability found in production → hotfix → downtime
✅ Shift Left:
Vulnerability caught during Pull Request → build fails → fixed before merge

🔁 Common Tools Used in Shift Left DevSecOps
SAST: SonarQube, Checkmarx
SCA: Snyk, Dependabot
IaC Scanning: Checkov, tfsec
Containers: Trivy
Policy: OPA, Kyverno

# How do you measure Shift-Left success?
Reduction in production incidents
Fewer critical vulnerabilities reaching prod
Faster MTTR
Higher PR-level detection rate

# What is Shift-Right Security in DevOps?
Shift-Right Security is a DevSecOps practice that focuses on security after deployment, i.e., in production and runtime, rather than only during design, coding, or CI/CD.
Shift-Right Security means continuously monitoring, detecting, and responding to security threats in live production environments using runtime visibility, real-time alerts, and automated remediation.


# You want a shallow clone — it grabs only the latest snapshot instead of the full history.
 $ git clone --depth 1 <repo-url>

Clone a specific branch only
git clone --depth 1 --branch main <repo-url>




3. What types of GitHub Actions runners have you used?
 1. GitHub Hosted Runner
Managed by GitHub.
Examples:
ubuntu-latest
windows-latest
macos-latest

 2. Self Hosted Runner
Hosted inside:
AWS EC2
Kubernetes
Azure VM
On-premise

Benefits:
Custom software
Internal network access
Better performance
Cost optimization

Interview Tip:
Mention you've configured self-hosted runners for deployment to private infrastructure.


4. What are GitHub Secrets?
 Secrets securely store sensitive information such as:
AWS keys
Docker credentials
API tokens
Kubernetes configs

Example:
env:
  AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}

Best practices:

Never hardcode credentials
Use OIDC instead of long-lived secrets
Rotate secrets regularly

When using GitHub Actions for CI/CD, the recommended place to store secrets is **GitHub Secrets** rather than hard-coding them in your repository or workflow files.

1. Repository Secrets - Available only to workflows in a specific repository.
 Settings → Secrets and variables → Actions → Repository secrets

2. Environment Secrets-
Useful when you have different environments such as dev, staging, and production.
 Settings → Environments → production → Secrets

3. Organization Secrets -
Shared across multiple repositories in a GitHub organization.
 Settings → Secrets and variables → Actions → Organization secrets 


5. What is OIDC in GitHub Actions?
   OIDC (OpenID Connect) allows GitHub Actions to authenticate directly with cloud providers without storing long-term credentials.



1. Question 1: How do you design and enforce the principle of least privilege across a large GitHub Enterprise footprint with multiple organizations?
   "I enforce the principle of least privilege by structuring access across three layers: the Enterprise level, the Organization level, and the Repository level. 
   First, I integrate our Identity Provider via SAML SSO and utilize Enterprise Managed Users (EMUs) to automate lifecycle management and offboarding.
   Instead of granting global administrative or organization owner permissions, I use Custom Organization Roles to provision fine-grained permissions for teams. 
   I restrict repository creation to specific templates, mandate branch protection rules via Enterprise Policies, and restrict third-party integrations by enforcing an OAuth app and GitHub App approval workflow to minimize external security vectors."

2. Question 2: A developer complains that their workflow is blocked because an unapproved third-party GitHub Action is being filtered out. How do you manage Actions policies securely?   
   "I control this through GitHub Actions Policies at the Enterprise or Organization level. 
   To maintain security without killing developer velocity, we disable 'Allow all actions' and instead select 'Allow select actions and reusable workflows'.
   If a team requires a new third-party action, they submit a request. 
   I check if the action is created by a Verified Creator or exists in our internal securely-forked marketplace. 
   If verified, I add the specific action SHA (rather than the mutable version tag like @v2) to our allowed list policy. This prevents supply chain attacks while keeping our pipelines secure."

3. Question 3: How do you provision and scale self-hosted GitHub Actions runners across both Linux and Windows platforms to guarantee high availability?
   "To ensure high availability and prevent resource starvation, I avoid static VM setups. 
   For Linux workloads, I leverage the Actions Runner Controller (ARC) deployed on Kubernetes. 
   ARC dynamically scales ephemeral runner pods up or down based on the webhook events received from GitHub Enterprise.
   
   For Windows workloads (often required for .NET frameworks), I provision auto-scaling groups in our cloud environment using Ansible to build out the base image. 
   I use a combination of persistent monitoring metrics (like queue depth via the GitHub API) and custom cleanup scripts to ensure these runners tear down or reset after handling job execution, guaranteeing a pristine, repeatable environment for the next CI/CD run."

4. Question 4: What security risks are associated with self-hosted runners, and how do you mitigate them?
   The primary risk with self-hosted runners is container breakout or malicious code execution on the host network, especially from public repositories or fork pull requests. 
   To mitigate this, 
   first, I segregate runners into distinct Runner Groups isolated by network firewalls, restricting their access to our internal production environment.
   
   Second, we strictly disable self-hosted runners for public repositories. 
   
   Third, we enforce the use of ephemeral runners—meaning the runner registration token is short-lived and the underlying container or instance is instantly destroyed right after a single job completes. 
   This ensures no malicious state or caching persists.

5. Question 5: How do you structure your Ansible playbooks and roles to ensure infrastructure consistency and idempotency across heterogeneous environments?
   I follow the standard Ansible role directory structure to separate logic, variables, and templates, ensuring reusability across Linux and Windows. 

   To handle multi-platform target nodes cleanly, I decouple the OS-specific tasks using include_tasks paired with ansible_os_family conditionals.

   Every task I write strictly adheres to idempotency—using modules like template instead of raw shell commands, and explicitly defining required states (state: present or state: started). 

   I also integrate Ansible-Lint into our pre-commit hooks and validate all configuration changes inside an isolated staging environment before rolling them out to production runners.

6. Question 6: Can you give an example of a custom Python automation script or integration you built to optimize developer workflows?
   I previously built a Python automation tool using the PyGithub library and GitHub REST APIs to streamline our organization's repository onboarding. 
   Manually configuring new repositories with branch protection, adding required team permissions, and setting up secret tokens was taking hours and leading to human error.
   I wrote a CLI script that takes an onboarding configuration file, automatically provisions the repo, applies strict branch protections (requiring status checks and PR reviews), syncs the requested AD groups, and injects default CI/CD variables. 
   This reduced our onboarding time from 2 hours to under 30 seconds while guaranteeing total policy compliance.   

7. Question 7: How do you monitor GitHub Enterprise platform efficiency, and what metrics matter most to you?
   I monitor platform health using two main lenses: Infrastructure health and Workflow efficiency. 
   I pull webhooks and audit logs into a centralized dashboard to track runner CPU/memory saturation and job failure rates.
   The core metrics I prioritize are Workflow Queue Time (how long a developer waits for a runner to pick up a job) and Build Duration. 
   If queue times spike, it triggers our autoscaler to spin up more runners. 
   If build duration increases, we analyze caching layers (like actions/cache) or network bottlenecks to optimize the platform efficiency.

8. Question 8: As a senior engineer, how do you approach mentoring junior DevOps engineers while maintaining high delivery standards?
   I foster a culture of continuous learning by balancing structured guidance with autonomy. 
   I avoid micro-management; instead, I champion comprehensive technical documentation, architecture design reviews, and pairing exercises.
   When junior team members work on infrastructure modules, we implement thorough Code Reviews and run validation tests in non-production sandboxes. 
   This gives them the confidence to build and deploy complex Ansible playbooks safely, knowing we have automated safety nets in place. 
   I also hold bi-weekly knowledge-sharing sessions where team members can present new automation tools or post-mortem lessons learned.   