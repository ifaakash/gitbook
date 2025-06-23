# Git Tag and changelog

Changelogs are used to track the changes that are made by different teams.

Tag store the configuration you have currently for the repository.

eg. 0.0.0 - two files in repo

0.0.1 - three files in repo. \[ now you have track of files using tags ]

We can create changelog in gitlab repo using the below approach:

1. initialise the repo
2. create a tag to start from eg. lets say 0.0.0 for now

{% hint style="info" %}
0.0.0
{% endhint %}

3. Now we have tag, so make the gitlab-ci.yml file to pick this tag as "from" and use it to generate the changelog file

```yaml
// Some code
stages:
  - tag-generate
  - changelog

variables:
  PRIVATE_TOKEN: "jYZrzyn9ESUtuN_JKFBR"
  test_branch: "master" 

create-changelog:
  stage: changelog
  image: ubuntu:latest
  script:
#    - version=$(cat VERSION.md)
#    - echo $version
    - apt-get update && apt-get install -y curl
    - 'curl --request POST --header "PRIVATE-TOKEN: $PRIVATE_TOKEN" --data "from=0.0.0&branch=$test_branch" "https://innersource.soprasteria.com/api/v4/projects/$CI_PROJECT_ID/repository/changelog?version=0.0.1"'
    # - 'curl --request POST --header "PRIVATE-TOKEN: $PRIVATE_TOKEN" --data "branch=$test_branch&message=Add new changelog; [skip ci]" --url "https://innersource.soprasteria.com/api/v4/projects/$CI_PROJECT_ID/repository/changelog?version=$version"'
    # - 'curl --request POST --header "PRIVATE-TOKEN: $PRIVATE_TOKEN" "https://innersource.soprasteria.com/api/v4/projects/$CI_PROJECT_ID/repository/tags?tag_name=$version&ref=$CI_COMMIT_MESSAGE&message=CI%20created%20tag"'


```

```yaml
// Some code
    - 'curl --request POST --header "PRIVATE-TOKEN: $PRIVATE_TOKEN" --data "from=0.0.0&branch=$test_branch" "https://innersource.soprasteria.com/api/v4/projects/$CI_PROJECT_ID/repository/changelog?version=0.0.1"'
```

4. make a new change and change the version as well so that the new tag is created in the changelog reflecting the change you have made.

```
// Some code
    - 'curl --request POST --header "PRIVATE-TOKEN: $PRIVATE_TOKEN" --data "from=0.0.1&branch=$test_branch" "https://innersource.soprasteria.com/api/v4/projects/$CI_PROJECT_ID/repository/changelog?version=0.0.2"'
```

this will see that you have updated the tag. We run the command again to have changes reflected in changelog file.

5. Once done, we can automate the whole process using below gitlab-ci.yml file

```
// Some code
create-changelog:
  stage: changelog
  image: ubuntu:latest
  script:
    - version=$(cat VERSION.md)
    - echo $version
    - apt-get update && apt-get install -y curl
    - 'curl --request POST --header "PRIVATE-TOKEN: $PRIVATE_TOKEN" --data "branch=$test_branch&message=Add new changelog; [skip ci]" --url "https://innersource.soprasteria.com/api/v4/projects/$CI_PROJECT_ID/repository/changelog?version=$version"'
```
