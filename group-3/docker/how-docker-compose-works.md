---
description: This page defines the working of docker-compose and related commands
icon: docker
---

# How docker-compose works?

* A docker compose file is used to deploy a set of services together.&#x20;
* In docker-compose file, we can manage the services + volumes + ports.&#x20;
* Using docker-compose file, we can deploy a micro-service using the pre-build images from [Dockerhub](https://hub.docker.com/) or we can build an image by referencing the build context

***

## Build context in Docker

Build context referes to the filesystem that will be used to store the files ( in the container ) and from the local filesystem. This is important when creating a Dockerfile or referencing in the docker-compose

{% hint style="info" %}
The build context can either be a local file system or it can be a remote repository as well. Refer below docs for reference

[https://docs.docker.com/build/concepts/context/](https://docs.docker.com/build/concepts/context/)
{% endhint %}

Consider the following directory structure:

```yaml
.
├── index.ts
├── src/
├── Dockerfile
├── package.json
└── package-lock.json

Dockerfile instructions can reference and include these files in the build if you pass this directory as a context.
```

```docker
FROM node:latest
WORKDIR /src
COPY package.json package-lock.json .
RUN npm ci
COPY index.ts src .

 docker build .
```

```docker
  frontend:
    build: ./frontend/
    ports:
      - 3000:3000
    depends_on:
      - backend
    networks:
      - fitbuddy

# Docker volume for DB and Backend service
volumes:
  fitbuddy_db_volume:

# Bridge network for communication between services
networks:
  fitbuddy:
```

means:

```
The build context for frontend is the ./frontend directory (relative to where the docker-compose.yml is located).
Docker will look for a Dockerfile inside that path by default (./frontend/Dockerfile).

You can override this by specifying:
```

```docker
build:
    context: ./frontend
    dockerfile: Dockerfile.frontend
```

***

## What does dot(.) refer in docker context?

The dot(.) refer to the local file system. It's the path where the docker-compose file exists, or the Dockerfile exists.&#x20;

{% hint style="info" %}
the `.` (dot) represents the **current directory**, and Docker sends **everything inside that directory** (recursively) to the Docker daemon as the build context.
{% endhint %}

> **Why is it Important?**
>
> Because:
>
> * Any file you want to **COPY** or **ADD** inside your image must exist **within the build context**.
> * Docker does **not** have access to files **outside** this context path.
> * The **context size** directly affects build performance (larger context = slower build).

## Example of Docker Compose

```
.
├── index.ts
├── src/
├── Dockerfile
├── package.json
└── package-lock.json
```

Here, if you run:

```docker
docker build .
```

then Docker sends this entire directory (the dot `.`) to the daemon as the context.

Inside the `Dockerfile`, commands like:

```docker
COPY package.json package-lock.json .
COPY index.ts src .
```

work **because** those files are **inside the context** (`.`).\
If you tried to copy a file from `../` (outside `.`), the build would fail — Docker restricts you to the context boundary.

## Best Practise

* Keep your Docker context **minimal**&#x20;
  * only include what’s required for the build
* **Ignore unnecessary files**
  * Use `.dockerignore` to exclude files/folders from the context to speed up builds.

***

## Docker compose commands

### Managing containers

<table><thead><tr><th width="230">Command</th><th>Description</th></tr></thead><tbody><tr><td><code>docker-compose up</code></td><td>Builds (if needed) and starts all services in the foreground (shows logs).</td></tr><tr><td><code>docker-compose up -d</code></td><td>Starts services in <strong>detached mode</strong> (background).</td></tr><tr><td><code>docker-compose down</code></td><td>Stops and removes containers, networks, and by default keeps volumes &#x26; images.</td></tr><tr><td><code>docker-compose down -v</code></td><td>Stops and <strong>removes volumes</strong> too. Useful for a clean slate.</td></tr><tr><td><code>docker-compose stop</code></td><td>Stops containers <strong>without removing them</strong> (they can be started again).</td></tr><tr><td><code>docker-compose start</code></td><td>Starts previously stopped containers (no rebuild).</td></tr><tr><td><code>docker-compose restart</code></td><td>Restarts all services.</td></tr></tbody></table>

### Building image

<table><thead><tr><th width="230">Command</th><th>Description</th></tr></thead><tbody><tr><td><code>docker-compose build</code></td><td>Builds or rebuilds images defined in the compose file.</td></tr><tr><td><code>docker-compose build --no-cache</code></td><td>Rebuilds images <strong>without using cache</strong> (fresh build).</td></tr><tr><td><code>docker-compose build &#x3C;service></code></td><td>Builds image for a specific service (e.g. <code>frontend</code>).</td></tr><tr><td><code>docker-compose pull</code></td><td>Pulls service images from Docker Hub (if you’re using pre-built ones).</td></tr></tbody></table>

### Debugging

<table><thead><tr><th width="229.5">Command</th><th>Description</th></tr></thead><tbody><tr><td><code>docker-compose ps</code></td><td>Lists containers created by the Compose project.</td></tr><tr><td><code>docker-compose logs</code></td><td>Shows logs from all services.</td></tr><tr><td><code>docker-compose logs -f</code></td><td>Follows (tails) the logs in real-time.</td></tr><tr><td><code>docker-compose logs &#x3C;service></code></td><td>Shows logs for a single service.</td></tr><tr><td><code>docker-compose top</code></td><td>Displays running processes of the containers.</td></tr><tr><td><code>docker-compose exec &#x3C;service> &#x3C;cmd></code></td><td>Execute a command in a running container (e.g. <code>docker-compose exec backend bash</code>).</td></tr><tr><td><code>docker-compose run &#x3C;service> &#x3C;cmd></code></td><td>Runs a <strong>one-time command</strong> in a new container. Useful for DB migrations, testing, etc.</td></tr></tbody></table>

### Cleanup

<table><thead><tr><th width="229.5">Command</th><th>Description</th></tr></thead><tbody><tr><td><code>docker-compose rm</code></td><td>Removes stopped containers.</td></tr><tr><td><code>docker-compose down --rmi all</code></td><td>Removes <strong>all images</strong> built or pulled by Compose.</td></tr><tr><td><code>docker-compose down -v --rmi all</code></td><td>Nukes everything — containers, volumes, and images. 🔥</td></tr><tr><td><code>docker-compose kill</code></td><td>Forcefully stops containers (sends <code>SIGKILL</code>).</td></tr></tbody></table>







