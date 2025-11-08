---
icon: ubuntu
---

# Homelab

<details>

<summary>IP Configuration</summary>

Access the Proxmox Web UI

```
https://192.168.1.50:8006
```

The credential for homelab are:

**U** - root

**P** - Jing

## Login to tailscale

[https://login.tailscale.com/admin/machines/](https://login.tailscale.com/admin/machines/)

</details>

<details>

<summary>VM Configuration</summary>

**IP**                              - 192.168.1.12

**U**                               - docker

**P**                               - docker

### Access from Local  Mac

* Goto warp
* SSH into the VM using
  * ssh docker@192.168.1.12
  * Copy the username and password from above
  * The current method is using the SSH keys&#x20;

```
ssh docker@192.168.1.12
The authenticity of host '192.168.1.12 (192.168.1.12)' can't be established.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.1.12' (ED25519) to the list of known hosts.
Enter passphrase for key '/Users/aakashmac/.ssh/id_ed25519': 
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 5.15.0-161-generic x86_64)
```

</details>

