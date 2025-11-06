# Add WSL as runner in Gitlab

If you want to register a machine ( consider my wsl machine ) as runner in two gitlab projects, then i need to run the "register" command twice.

**Register as a Specific Runner for Another Project**: You can also register the same WSL machine as a **specific runner** for other projects by using a different token for each project. When registering a new runner, follow these steps:

* Run the registration command on your WSL machine (`gitlab-runner register`).
* Provide the new project’s URL and the registration token from the other GitLab project.
* Configure the runner with a new name to differentiate it from others.



Some important path related to runner in linux machine:

{% hint style="info" %}
"/etc/gitlab-runner/config.toml"

/usr/local/bin/gitlab-runner
{% endhint %}

```
// Some code

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
    volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"]
    shm_size = 0
    network_mtu = 0

[[runners]]
  name = "ITEM-S119911"
  url = "https://innersource.soprasteria.com/"
  id = 1709913
  token = "LyCpRqs4rb14AQpXwUqh"
  token_obtained_at = 2024-09-06T19:27:36Z
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
    privileged = false
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/cache"]
    shm_size = 0
    network_mtu = 0
```
