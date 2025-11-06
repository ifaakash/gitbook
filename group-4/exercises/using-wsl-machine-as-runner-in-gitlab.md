# Using WSL machine as runner in gitlab

Register new gitlab runner

&#x20;1\. [https://docs.gitlab.com/runner/install/linux-manually.html#using-binary-file](https://docs.gitlab.com/runner/install/linux-manually.html#using-binary-file)

{% hint style="info" %}
sudo curl -L --output /usr/local/bin/gitlab-runner "https://s3.dualstack.us-east-1.amazonaws.com/gitlab-runner-downloads/latest/binaries/gitlab-runner-linux-amd64"
{% endhint %}

follow this URL: [https://docs.gitlab.com/runner/install/bleeding-edge.html#download-one-of-the-packages-for-debian-or-ubuntu](https://docs.gitlab.com/runner/install/bleeding-edge.html#download-one-of-the-packages-for-debian-or-ubuntu)

2.  ```
    sudo chmod +x /usr/local/bin/gitlab-runner

    Create a GitLab CI user:

    sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash


    Install and run as service:

    sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
    sudo gitlab-runner start

    Register the runner
    GR13489417oWoszc3Lzyy9wdgYa2v -- glrt [ gitlab runner regis. token ]
    ```

    \
    config.toml file for runner on WSL:

```
shutdown_timeout = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "docker-runner"
  url = "https://innersource.soprasteria.com/"
  id = 1709845
  token = "4g4gCBX1jY43KGs5Zize"
  token_obtained_at = 2024-09-05T10:49:26Z
  token_expires_at = 0001-01-01T00:00:00Z
  executor = "docker"
  [runners.custom_build_dir]
  [runners.cache]
    MaxUploadedArchiveSize = 0
    [runners.cache.s3]
    [runners.cache.gcs]
    [runners.cache.azure]
  [runners.docker]
    tls_verify = false
    image = "docker:stable"
    privileged = true
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/cache"]
    shm_size = 0
    network_mtu = 0
```

Commands:

1. sudo gitlab-runner start



before \[ config.toml file ]

```
// Some code
[[runners]]
  ...
  [runners.docker]
    volumes = ["/cache"]
    ...
  
```

```
// Some code
[[runners]]
  ...
  [runners.docker]
    volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"]
    ...
```
