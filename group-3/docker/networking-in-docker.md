---
icon: docker
---

# Networking in Docker

### Docker Network Types

Docker has 5 main network types:

#### 1. **Bridge Network** (Default)

The most common network type. Creates an isolated network on the host.

**How it works:**

* Containers get their <mark style="background-color:yellow;">own internal IP</mark> (like 172.17.0.x)
* <mark style="background-color:yellow;">Can talk to each other using container names</mark>
* Need <mark style="background-color:yellow;">port mapping to access from outside</mark>

**Example Scenario:**

<pre class="language-yaml"><code class="lang-yaml"># docker-compose.yml
version: '3.8'

services:
  web:
    image: nginx
<strong>    ports:
</strong>      - "8080:80"  # Maps container port 80 to host port 8080
    networks:
      - my-app-network
  
  api:
    image: my-api
    networks:
      - my-app-network

networks:
  my-app-network:
    driver: bridge
</code></pre>

**Access pattern:**

* Inside Docker: `web` can reach `api` via `http://api:port`
* From host/VM: Access web via `http://VM_IP:8080`
* From outside VM: Access via `http://VM_IP:8080` (if VM allows it)

**Use when:** Standard multi-container apps on a single host

***

#### 2. **Host Network**

<mark style="background-color:yellow;">Container shares the host's network directly. NO network isolation.</mark>

**Example:**

```yaml
services:
  web:
    image: nginx
    network_mode: "host"  # Uses VM's network directly
```

**What happens:**

* Container uses VM's IP directly
* If nginx runs on port 80, it's immediately available at `VM_IP:80`
* NO port mapping needed (`ports:` is ignored)
* Container sees ALL network interfaces of the VM

**Use when:**

* Maximum network performance needed
* You want the container to act like it's running directly on the VM
* Monitoring tools that need to see host network
* Testing/debugging network issues

**⚠️ Downsides:**

* No network isolation (security concern)
* Port conflicts if multiple containers want same port
* Only works on Linux hosts

***

#### 3. **None Network**

Container has NO network access at all.

**Example:**

```yaml
services:
  secure-processor:
    image: data-processor
    network_mode: "none"
```

**Use when:**

* <mark style="background-color:yellow;">Maximum security isolation needed</mark>
* <mark style="background-color:purple;">Batch processing jobs</mark> that don't need network
* Testing in complete isolation
* Processing sensitive data offline

***

#### 4. **Overlay Network**

For multi-host networking (Docker Swarm/<mark style="background-color:red;">Kubernetes</mark>).

**Example Scenario:**

```yaml
# Running on multiple VMs
version: '3.8'

services:
  web:
    image: nginx
    deploy:
      replicas: 3
    networks:
      - multi-host-network

networks:
  multi-host-network:
    driver: overlay
```

**What happens:**

* <mark style="background-color:yellow;">Containers on different VMs can talk to each other</mark>
* Appears as one logical network across multiple hosts
* Automatic service discovery

**Use when:**

* Docker Swarm cluster
* Microservices across multiple VMs
* High availability setups

***

#### 5. **Macvlan Network**

Container gets its own MAC address and appears as physical device on network.

**Example:**

```yaml
networks:
  macvlan-net:
    driver: macvlan
    driver_opts:
      parent: eth0  # Your VM's network interface
    ipam:
      config:
        - subnet: 192.168.1.0/24
          gateway: 192.168.1.1

services:
  device:
    image: my-device
    networks:
      macvlan-net:
        ipv4_address: 192.168.1.50  # Gets real IP on your network
```

**What happens:**

* Container gets an IP on your physical network (like 192.168.1.50)
* Appears as a separate device on your LAN
* Can be accessed directly without port mapping

**Use when:**

* Legacy apps expecting to be directly on network
* Network appliances/monitoring tools
* Need multiple IPs on containers
* IoT or network simulation

***

### Your Specific Question

> "Container on VM, expose using port, access via VM IP"

**Answer: Bridge Network** (the default)

Here's exactly what you'd do:

```yaml
version: '3.8'

services:
  myapp:
    image: my-application
    ports:
      - "8080:80"  # Container port 80 → VM port 8080
    networks:
      - bridge-net

networks:
  bridge-net:
    driver: bridge
```

**Access flow:**

1. Container runs on internal bridge network (e.g., 172.17.0.2:80)
2. Docker maps VM's port 8080 → container's port 80
3. You access it via `http://VM_IP:8080`
4. Docker forwards traffic to container

**If you want ANY port on VM to work:**

```bash
# Run container with host network
docker run --network host my-image
```

Then access directly via `VM_IP:80` (whatever port the app uses)

***

### Quick Decision Guide

**Choose Bridge when:**

* Standard applications
* Multiple containers on one host
* Need network isolation
* Your use case (VM with port mapping)

**Choose Host when:**

* Need maximum performance
* <mark style="background-color:yellow;">Want container to behave exactly like VM process</mark>
* Don't care about network isolation

**Choose None when:**

* Complete isolation needed
* No network required

**Choose Overlay when:**

* Multiple VMs/hosts
* Docker Swarm

**Choose Macvlan when:**

* Need container to have real IP on physical network
* Legacy applications

For your scenario, **stick with bridge** - it's the standard, secure way to expose container ports via your VM's IP!
