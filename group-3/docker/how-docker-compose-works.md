---
description: This page defines the working of docker-compose and related commands
icon: docker
---

# How docker-compose works?

* A docker compose file is used to deploy a set of services together.&#x20;
* In docker-compose file, we can manage the services + volumes + ports.&#x20;
* Using docker-compose file, we can deploy a micro-service using the pre-build images from [Dockerhub](https://hub.docker.com/) or we can build an image by referencing the build context

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

