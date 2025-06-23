# EOF doesn't work in gitlabci

```yaml
// Some code
install-pip-alternative:
  tags:
    - wsl-runner
  image: ubuntu:latest
  stage: deploy
  script:
    - echo "Logging to VM"
    - apt update && apt install -y openssh-client
    - chmod 600 slazacvm01PEM.pem
    - ssh -o StrictHostKeyChecking=no -i slazacvm01PEM.pem ubuntu@20.13.0.150 \
      "echo 'Logged into VM' && ls && pwd && sudo apt update && apt install -y python3-pip && echo 'PIP installed' && pip3 --version"
    - echo "EXITED VM -- back to pipeline"
```

the below code doesnt work with gitlab-ci.yml file

```yaml
// Some code

.install-pip:
  tags:
    - wsl-runner
  image: ubuntu
  stage: deploy
  script:
    - echo "Logging to VM"
    - apt update && apt install -y openssh-client
    - chmod 600 slazacvm01PEM.pem 
    - ssh -o StrictHostKeyChecking=no -i slazacvm01PEM.pem ubuntu@20.13.0.150 << 'EOF'
      echo "Logged into VM"
      ls
      pwd
      sudo apt update
      sudo apt install python3-pip
      echo "PIP installed"
      pip3 --version
      'EOF'
    - echo "EXITED VM -- back to pipeline"
```
