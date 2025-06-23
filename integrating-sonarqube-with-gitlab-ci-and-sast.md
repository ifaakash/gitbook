# Integrating sonarqube with gitlab-ci and SAST

Integration sonarqube with gitlab-ci requires you to deploy a sonarqube instance.&#x20;

1. Deploy the sonarqube instance.
2. once deployed, access the instance and select to configure CI/CD via gitlab.
3. It will give you the option to import jobs from GitLab using your personal access token.
4. Give your personal access token to access all the GitLab projects inside sonarqube.&#x20;
5. Once your token can import all your projects, then you need to select the project.
6. Follow the steps to configure sonarqube inside the project.
   1. It will ask to create variable like SONAR\_HOST\_URL & SONAR\_TOKEN
7. once done, you will get the script to add in your GitLab-ci.yml file to integrate SONARQUBE with your pipeline.



The SAST stage is automatically added once you enable the option from the projects setting.

SECURE -> SECURITY CONFIGURATIONS -> Static Application Security Testing (SAST) \[ enable this ]



Below is the code added for SAST:

```yaml
sast:
  stage: test
include:
- template: Security/SAST.gitlab-ci.yml
```

```yaml

# You can override the included template(s) by including variable overrides
# SAST customization: https://docs.gitlab.com/ee/user/application_security/sast/#customizing-the-sast-settings
# Secret Detection customization: https://docs.gitlab.com/ee/user/application_security/secret_detection/pipeline/#customization
# Dependency Scanning customization: https://docs.gitlab.com/ee/user/application_security/dependency_scanning/#customizing-the-dependency-scanning-settings
# Container Scanning customization: https://docs.gitlab.com/ee/user/application_security/container_scanning/#customizing-the-container-scanning-settings
# Note that environment variables can be set in several places
# See https://docs.gitlab.com/ee/ci/variables/#cicd-variable-precedence
stages:
- sonar-analysis
- test
sonar-analyze:
  stage: sonar-analysis
  image: sonarsource/sonar-scanner-cli:latest
  variables:
    SONAR_USER_HOME: "${CI_PROJECT_DIR}/.sonar"
  cache:
    key: "${CI_JOB_NAME}"
    paths:
    - ".sonar/cache"
  script:
  - sonar-scanner
  allow_failure: true
  only:
  - master
sast:
  stage: test
include:
- template: Security/SAST.gitlab-ci.yml

```
