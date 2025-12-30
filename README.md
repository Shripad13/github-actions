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
   

'''
Static Code Analysis - Pushed to the repo (SonarQube tool)
SAST                 - Overall Security Posture of code (CheckMarx)
Software Composition Analysis - Open Source libraries Vulnerability (CheckMarx SCA)
DAST - endpoint of application can be tested (Whitebox testing (OWASP ZAP)
'''


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

Installl Github CLI on the CI-Runner using ansible.
$ curl -fsSL https://raw.githubuser | sudo bash