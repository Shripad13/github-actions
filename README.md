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