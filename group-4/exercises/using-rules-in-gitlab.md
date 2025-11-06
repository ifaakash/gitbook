# Using rules in Gitlab

1. Rules are used to make the jobs specific to the branch.&#x20;
2. When I commit to a particular branch, then particular jobs should be triggered.
3. This can be achieved using "rules", "only" and "extends".&#x20;

Rules are used to make commits as branch-specific.

example:

consider a job "job-1" which should be triggered only if the commit is done on the development branch. then we can create a rule here and attach it to this job.

```yaml
stages:
    - job-1

.rules-based-triggers:
    rules:
        - &rule_A:
          if: "$CI_COMMIT_BRANCH == 'master'"

trigger-in-development-only:
    stage: job-1
    script:
        - echo ""
    rules:
        - *rule_A
```

```yaml

stages:
  - dev-branch
  - master-branch
  - feat-rollout-branch

.only-master-and-development:
  rules:
    - &rule_A
      if: '$CI_COMMIT_BRANCH == "master" || $CI_COMMIT_BRANCH == "development"'

trigger-in-dev:
  stage: dev-branch
  script:
    - echo "This is the DEVELOPMENT branch"
  only:
    - development

trigger-in-master:
  stage: master-branch
  script:
    - echo "Triggered in MASTER branch"
  rules: 
    - if: '$CI_COMMIT_BRANCH == "master"'

trigger-in-master-and-development:
  stage: feat-rollout-branch
  script:
    - echo "This is running in MASTER and DEVELOPMENT branch using 'extends'"
  rules: 
    - *rule_A


```
