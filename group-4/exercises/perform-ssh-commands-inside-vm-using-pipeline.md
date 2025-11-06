# Perform SSH commands inside VM using pipeline

To perform SSH command inside VM, the EOF doesnt work. For that, you need to use '\\' to enter commands in next line

Along with that, use '&&' to input a series of command to perform using SSH connection inside VM

```yaml
// Some code
ssh -o StrictHostKeyChecking=no -i slazacvm01PEM.pem ubuntu@<IP-ADDRESS> \
"echo 'Logging to VM' && ls && pwd "
```



Once you have logged into the VM using SSH in a script (like in GitLab CI), the commands after the SSH line will not execute on the remote VM automatically; instead, they will continue executing locally after SSH completes.

To perform tasks inside the VM, you need to pass the commands directly to `ssh`. You can do this by providing the commands to execute after the SSH connection in the same command line.

Here’s how you can modify your script to perform tasks inside the VM:

#### Example GitLab CI Script:

```yaml
yamlCopy codescript:
  - echo "Logging to VM"
  - apk update && apk add openssh
  - chmod 600 slazacvm01PEM.pem
  - ssh -o StrictHostKeyChecking=no -i slazacvm01PEM.pem ubuntu@20.13.0.150 << EOF
      echo "@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@"
      echo "LOGGED IN TO VM"
      echo "@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@"
      # Add your tasks here, e.g., update packages, run a script, etc.
      sudo apt update
      sudo apt install -y nginx
      echo "Nginx installed"
      # You can add any other commands you want to execute inside the VM
EOF
  - echo "Exited VM"
```

#### Explanation:

* **SSH Command with EOF Block**: The `ssh` command is followed by `<< EOF`, which allows you to send multiple commands inside the SSH session. Everything between `EOF` and the closing `EOF` will be executed on the remote VM.
* **Remote Commands**: In this case, tasks like `sudo apt update` and `sudo apt install nginx` are run inside the VM. You can replace these with any tasks you need.

Once all commands inside the `EOF` block are executed, you will exit the SSH session and continue with the rest of the pipeline.

