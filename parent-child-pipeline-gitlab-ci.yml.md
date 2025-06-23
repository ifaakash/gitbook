---
description: >-
  Repository Link:
  https://innersource.soprasteria.com/gitlab-training/training-3
---

# Parent-child pipeline gitlab-ci.yml

We need to have two gitlab-ci.yml files for each parent and child

Parent gitlab-ci.yml

```yaml

stages:
  - parent-pipeline

parent-stage:
  stage: parent-pipeline
  script:
    - echo "This is the parent pipeline"

child-pipeline-1:
  stage: parent-pipeline
  trigger:
    include: .child-gitlabci.yml
    strategy: depend
```

Child gitlab-ci.yml

```yaml

stages:
  - hello-message-from-child

hello-message-from-child:
  stage: hello-message-from-child
  script:
    - echo "Hello from CHILD pipeline"
```

The parent pipeline calls the child with the help of "include" variable.&#x20;

The trigger button helps to include the child pipeline ".child-gitlab-ci.yml" file.

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption><p>parent-child pipeline setup</p></figcaption></figure>

NOTE:

{% hint style="info" %}
The child pipeline must have one job in it

stages:

&#x20;\- stg1

first-job: \[ this is the job ]

&#x20; stage: stg1
{% endhint %}

MORE RE-DEFINED APPROACH:\


```yaml
// Some code
stages:
  - packages

# Package A configuration
packageA:
  stage: packages
  # `trigger` is the keyword to create a child pipeline
  trigger:
    # Include the configuration file of the child pipeline
    include: packageA/.gitlab-ci.yml
    # With this strategy, the parent only succeed if the child succeed too
    strategy: depend

# Package B configuration
packageB:
  stage: packages
  trigger:
    include: packageB/.gitlab-ci.yml
    strategy: depend

# Package C configuration
packageC:
  stage: packages
  trigger:
    include: packageC/.gitlab-ci.yml
    strategy: depend
```



THE CHILD PIPELINE:

```yaml
// Some code

stages:
  - run

job:
  stage: run
  script:
    - echo "I am package A"
```



MULTI-CHILD parent pipeline:

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption><p>multi-child to single parent pipeline setup</p></figcaption></figure>

```yaml
// Some code
stages:
  - parent-pipeline

parent-stage:
  stage: parent-pipeline
  script:
    - echo "This is the parent pipeline"

child-pipeline-1:
  stage: parent-pipeline
  trigger:
    include: .child-gitlabci.yml
    strategy: depend

child-pipeline-2:
  stage: parent-pipeline
  trigger:
    include: .secondchild-gitlabci.yml
    strategy: depend
```

```yaml
// Some code

stages:
  - hello-message-from-secondchild
  - hello-message-2.O

hello-from-second-child:
  stage: hello-message-from-secondchild
  script:
    - echo "Hello from SECOND CHILD pipeline"
```
