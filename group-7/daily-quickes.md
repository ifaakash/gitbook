---
description: This page include all the quickies done on a particular day
icon: atom
---

# Daily Quickes

<details>

<summary>6th Nov</summary>

## Virtualization and running Docker on Windows VM

To run docker, we need to have virtualization enabled on the VM. Generally, most of the instance type on AWS dont have virtualization enabled, that's when the bare metal instance comes into play.&#x20;

{% hint style="info" %}
Some instance type do give the virtualization support like c5 instance ( NOT YET TESTED )
{% endhint %}

### How to use a bare metal instance?

Firstly, we need to create a dedicated host. This allocates a bare metal instance to you, that you have full control over. You will need to define the size of the dedicated host and you are good to go.

Once the dedicated host is deployed, you will need to launch a new instance onto this dedicated host.

Select the dedicated host, and launch a new instance ( the max size for this can be the size of the dedicated host )

### Docker Images for containerizing PHP application

For a multi-stage build, we first build the basic dependencies of the PHP application. This is done using an image named "[compose](https://hub.docker.com/_/composer)" – this helps to install the required dependencies. We generally run this after copying the _**composer.json**_ and _**composer.lock**_ file.&#x20;

```
FROM composer:latest@xyz
WORKDIR /dependencies
COPY composer.json composer.lock ./
RUN composer install 
```

Once the dependencies are copied, we have two options to run the PHP image:

* Run using  `php -S localhost:9000`&#x20;
* Run using  `php-fpm`&#x20;

```
CMD ["php-fpm"] OR CMD ["php", "-S", "localhost:9000"]
```

#### What is FPM?

FPM stands for FastCGI process manager. This only loads the PHP application and doesn't serve the HTTP traffic. To serve the HTTP traffic, we will need nginx in between this setup.

<mark style="background-color:purple;">Client -> Nginx -> PHP-FPM</mark>

### Important files identifies in MODX CMD application

[**MODX CMD** ](https://modx.com/)**->** CMS stands for content management system. This is like wordpress, used to curate website, but, here the developer have full control over the code. This give PHP code, that is customizable. MODX is open-source.

Till now, I have identified the below files:

```
.
└── ./HOSTINGERBACKUP/
    ├── ./HOSTINGERBACKUP/public_html/
    │   └── ./HOSTINGERBACKUP/public_html/index.php
    ├── ./HOSTINGERBACKUP/core/
    │   └── ./HOSTINGERBACKUP/core/config.php.inc
    ├── ./HOSTINGERBACKUP/controllers
    ├── ./HOSTINGERBACKUP/docker-compose.yml
    ├── ./HOSTINGERBACKUP/Dockerfile
    └── ./HOSTINGERBACKUP/nginx.conf
```

### Build context in Docker compose file

Lets say i have below Dockerfile

```
FROM nginx:latest@xyz
WORKDIR /dependencies --> This is the working directory in container
COPY . ./ 
```

### Volumes in Docker compose

```
services:
  nginx:
    build:
      context: . --> This mentions the root filesystem ( in local machine )
    volumes:
      - ./nginx.conf:./dependencies/configuration_file
volumes:
  db_data:
```

<mark style="background-color:purple;">./nginx.conf</mark> <mark style="background-color:purple;"></mark><sup><mark style="background-color:purple;">**( local filesystem )**<mark style="background-color:purple;"></sup><mark style="background-color:purple;">: ./dependencies/configuration\_file</mark> <mark style="background-color:purple;"></mark><sup><mark style="background-color:purple;">**( container filesystem )**<mark style="background-color:purple;"></sup>

Build context decide what will be used as the root filesystem, by the docker containers. Also, whem building any docker image, the build context is of most importance, as it contains the Dockerfile

</details>

<details>

<summary>7th Nov</summary>

### How does nginx works to route traffic to PHP application, if they are running together using docker-compose?



### How to mount a sql dump to a mariadb database OR to a mysql container in docker-compose file?

</details>







<details>

<summary>9th Nov</summary>

### Difference between expose and ports in docker compose

### The Key Difference

**`expose`**: Makes <mark style="background-color:yellow;">ports accessible ONLY to other containers</mark> in the same Docker network. NOT accessible from your host machine.

**`ports`**: Maps <mark style="background-color:yellow;">container ports to your host machine</mark>, making them accessible from outside Docker (your browser, localhost, external clients).

### Practical Example Scenario

Let's say you're building a web application with:

* A **frontend** (React app on port 3000)
* A **backend API** (Node.js on port 5000)
* A **database** (PostgreSQL on port 5432)

```yaml
version: '3.8'

services:
  frontend:
    image: my-react-app
    ports:
      - "3000:3000"  # Accessible from host at localhost:3000
    depends_on:
      - backend

  backend:
    image: my-node-api
    ports:
      - "5000:5000"  # Accessible from host at localhost:5000
    expose:
      - "5000"       # Also accessible to frontend container
    depends_on:
      - database

  database:
    image: postgres:15
    expose:
      - "5432"       # ONLY accessible to backend, NOT from host
    environment:
      POSTGRES_PASSWORD: secret
```

### What's Happening Here?

1. **Frontend** uses `ports` because users need to access it from their browser at `localhost:3000`
2. **Backend** uses both:
   * `ports` so you can test API directly at `localhost:5000` during development
   * `expose` is actually redundant here (ports already exposes it internally)
3. **Database** uses only `expose` because:
   * Backend can connect to it via `database:5432` (service name)
   * Your host machine CANNOT access it (security!)
   * No external access needed

### Best Practices

**Use `expose` when:**

* <mark style="background-color:yellow;">Service only needs inter-container communication</mark>
* Security-sensitive services (databases, caches, internal APIs)
* Production environments where you don't want direct host access

**Use `ports` when:**

* <mark style="background-color:yellow;">You need to access the service from your host</mark> (browser, Postman, etc.)
* Front-facing services (web servers, APIs meant for external access)
* Development environments where you need to debug/test directly

### Better Production Example

```yaml
version: '3.8'

services:
  nginx:
    image: nginx
    ports:
      - "80:80"      # Only entry point from outside
    
  api:
    image: my-api
    expose:
      - "8080"       # Only nginx can reach it
    
  redis:
    image: redis
    expose:
      - "6379"       # Only api can reach it
    
  postgres:
    image: postgres
    expose:
      - "5432"       # Only api can reach it
```

In this setup, **only nginx** is exposed to the outside world. Everything else is locked down internally. This is the most secure approach.

### Common Mistake to Avoid

Don't do this in production:

```yaml
postgres:
  ports:
    - "5432:5432"  # ❌ Database exposed to host = security risk!
```

Do this instead:

```yaml
postgres:
  expose:
    - "5432"  # Only accessible within Docker network
```

The key insight: **`expose` is about documentation and internal networking, `ports` is about external access.**&#x20;

### How to define Database URL in .env file, when multiple containers are running



</details>
