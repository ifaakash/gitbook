# Login to VM

1. Set Up an EC2 Instance and Configure SSH Access

* Go to AWS Management Console.
* Launch an EC2 instance (e.g., Ubuntu 20.04).
* Configure the security group to allow SSH access (Port 22).

2.  **Generate SSH Key** (if you don't already have one):

    ```
    ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

    ```

* This will generate a public and private SSH key.
* The key will be available at /home/ubuntu/.ssh/\<name\_of\_the\_key>

3.  **Copy the Public Key to EC2**:

    * Copy your public key to the EC2 instance to allow password-less access.

    ```
    ssh-copy-id -i ~/.ssh/id_rsa.pub ubuntu@<EC2_INSTANCE_IP>
    ```

* Alternatively, you can manually paste the public key into the `~/.ssh/authorized_keys` file on the EC2 instance.

4. **Test SSH Access**:
   *   Test if you can SSH into your EC2 instance.

       ```
       ssh ubuntu@<EC2_INSTANCE_IP>
       ```





DEPLOY TO EC2

```yaml
// Some code
# Deploy to EC2 stage
deploy:
  stage: deploy
  only:
    - main   # You can change this to the branch you want to deploy from
  before_script:
    - 'which ssh-agent || ( apt-get update -y && apt-get install openssh-client -y )'
    - eval $(ssh-agent -s)
    - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add -
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh
    - ssh-keyscan -H <EC2_INSTANCE_IP> >> ~/.ssh/known_hosts

  script:
    # Sync files to EC2 instance using scp or rsync
    - scp -r ./your_app_directory ubuntu@<EC2_INSTANCE_IP>:/var/www/your_app
    # SSH into EC2 and run deployment script (e.g., install dependencies, restart services)
    - ssh ubuntu@<EC2_INSTANCE_IP> 'bash /var/www/your_app/deploy.sh'
```



SHORT STEPS:

1. deploy the VM
2. generate a private key file for the VM
3. Download the .pem file \[ private key file ]
4. once you have the private key file, ssh to the VM using your local terminal.

```

ssh -i ~/.ssh/id_rsa.pem ubuntu@20.13.0.150

```
