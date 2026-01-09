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
Job - Job can have number of steps
Step - A step can be an action or a script to be executed
Action - An action is a reusable extension that can be used in a step
Runners - will be maintained by you.

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
Github offers, "gh cli" using from the ci-runner connect eith github, get the token & run.

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

Because we will have Base image in Docker image (node image/alpine image) that hsould not contain vulnerabilities & issues

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
2. We can deploy rometheus & grafana on the same EKS Cluster

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