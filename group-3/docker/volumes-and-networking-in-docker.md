---
description: This page covers the concept about how volumes and network works in Docker
icon: docker
---

# Volumes and Networking in Docker

## Volumes

Volumes are persistent data stores for containers. When we create a volume and mount that to container, this volume is present on the docker host, and is available within a directory on docker host.

You can create a volume using&#x20;

```
docker volume create <volume-name>
```

> <mark style="background-color:yellow;">This is similar to the way that bind mounts work, except that volumes are managed by Docker and are isolated from the core functionality of the host machine.</mark>

Volumes are <mark style="background-color:purple;">managed by Docker in its internal storage</mark> (`/var/lib/docker/volumes/...`)

## Use case of Volumes

* Volumes are easier to back up or migrate than bind mounts.
* You can manage volumes using Docker CLI commands or the Docker API.
* Volumes work on both Linux and Windows containers.
* Volumes can be more safely shared among multiple containers.
* New volumes can have their content pre-populated by a container or build.
* When your application requires high-performance I/O

{% hint style="success" %}
Volumes are not a good choice if you need to access the files from the host, as the volume is completely managed by Docker. Use [bind mounts](https://docs.docker.com/engine/storage/bind-mounts/) if you need to access files or directories from both containers and the host.
{% endhint %}

***

## The Core Difference

<table data-header-hidden><thead><tr><th width="170"></th><th></th><th></th></tr></thead><tbody><tr><td><strong>Feature</strong></td><td><strong>Bind Mount</strong></td><td><strong>Volume</strong></td></tr><tr><td><strong>Where data lives</strong></td><td>On the host filesystem — <em>exact</em> path you specify (e.g. <code>/home/aakash/data</code>)</td><td>Managed by Docker in its internal storage (<code>/var/lib/docker/volumes/...</code>)</td></tr><tr><td><strong>Created by</strong></td><td>You (manual path)</td><td>Docker (automatically)</td></tr><tr><td><strong>Who manages it</strong></td><td>You</td><td>Docker</td></tr><tr><td><strong>Use case</strong></td><td>Tight integration with host system, e.g., local code editing, config files</td><td>Persistent data for containers (databases, app data)</td></tr><tr><td><strong>Portability</strong></td><td>Not portable; tied to host path</td><td>Portable; Docker can manage and move them</td></tr><tr><td><strong>Security</strong></td><td>Less isolated; container can access arbitrary host paths</td><td>More isolated; managed within Docker’s namespace</td></tr><tr><td><strong>Backups &#x26; Restore</strong></td><td>Manual</td><td>Easy to manage using <code>docker volume</code> commands</td></tr></tbody></table>

***

#### Example

**Bind Mount**

```bash
docker run -d \
  --name my-nginx \
  -v /home/aakash/mywebsite:/usr/share/nginx/html \
  nginx
```

* Host path: `/home/aakash/mywebsite`
* Container path: `/usr/share/nginx/html`
* Any change you make on your local filesystem **immediately reflects** inside the container.

**Use case:** Local development where you want live changes.

***

**Volume**

```bash
docker run -d \
  --name my-db \
  -v mydata:/var/lib/mysql \
  mysql
```

* Volume name: `mydata`
* Docker stores it at `/var/lib/docker/volumes/mydata/_data`
* Docker manages the lifecycle of that directory.

**Use case:** Database or persistent application data that should outlive container restarts.

***

#### Commands You’ll Commonly Use

**For Volumes:**

```bash
docker volume create mydata
docker volume ls
docker volume inspect mydata
docker volume rm mydata
```

**For Bind Mounts:**\
You don’t “create” them through Docker — they exist as regular directories on your host.

***

#### In Short

> **Bind mounts** = “Use this exact folder from my host.”\
> **Volumes** = “Docker, please handle this persistent storage for me.”

***

## Pro Tip

* **Development:** Use **bind mounts** — easy to sync local code changes.
* **Production:** Use **volumes** — safer, cleaner, and easier to migrate or back up.

***

## Temporary Mounts

**Volumes are often a better choice than writing data directly to a container, because a volume doesn't increase the size of the containers using it.**&#x20;

Using a volume is also faster; **writing into a container's writable layer** requires a [storage driver](https://docs.docker.com/engine/storage/drivers/) to manage the filesystem. The storage driver provides a union filesystem, using the Linux kernel. This extra abstraction reduces performance as compared to using volumes, which write directly to the host filesystem.

If your container generates **non-persistent state data, consider using a** [**tmpfs mount**](https://docs.docker.com/engine/storage/tmpfs/) to avoid storing the data anywhere permanently, and to increase the container's performance by avoiding writing into the container's writable layer.

***

## Does Volumes gets Deleted when the container is removed?

#### Default Behavior

If you remove a container **normally**, the **Docker-managed volume persists**.\
That’s right — deleting the container **does not delete the volume** by default.

Example:

```bash
docker run -d \
  --name my-db \
  -v mydata:/var/lib/mysql \
  mysql
```

Now if you remove the container:

```bash
docker rm -f my-db
```

Volume `mydata` still exists!\
You can confirm:

```bash
docker volume ls
```

Output:

```
DRIVER    VOLUME NAME
local     mydata
```

So you can even attach it to another container:

```bash
docker run -d -v mydata:/var/lib/mysql --name new-db mysql
```

All the previous data will still be there.

***

#### When It _Does_ Get Deleted

Only if you explicitly tell Docker to remove it using the `-v` flag:

```bash
docker rm -v my-db
```

or

```bash
docker compose down -v
```

That’s Docker’s way of saying:

> “Hey, I know this volume is shared storage, but please delete it _along with the container_.”

***

#### Why It Persists by Default

Because volumes are designed for **data persistence** and **decoupling** from containers.\
A container is ephemeral (throwaway), but volumes are not — they’re **first-class citizens** managed by Docker to store important data (like databases, uploads, configs, etc.).

***

#### 🧠 TL;DR

<table><thead><tr><th>Action</th><th width="179">Volume persists?</th><th>Notes</th></tr></thead><tbody><tr><td><code>docker rm container</code></td><td>✅ Yes</td><td>Default safe behavior</td></tr><tr><td><code>docker rm -v container</code></td><td>❌ No</td><td>Volume explicitly removed</td></tr><tr><td><code>docker compose down</code></td><td>✅ Yes</td><td>Keeps data</td></tr><tr><td><code>docker compose down -v</code></td><td>❌ No</td><td>Deletes data too</td></tr></tbody></table>

***





