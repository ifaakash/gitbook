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

<details>

<summary>12th Nov</summary>

{% code lineNumbers="true" %}
```yaml
docker@docker:~/Fitbuddy_backup$ docker run -it --rm -v $(pwd)/backup:/fit-backup-volume -v fitbuddy_fitbuddy_db_volume:/fit-backup-volume alpine sh
docker: Error response from daemon: Duplicate mount point: /fit-backup-volume

docker@docker:~/Fitbuddy_backup$ docker run -it --rm -v $(pwd)/backup:/fit-backup-volume -v fitbuddy_fitbuddy_db_volume:/to alpine sh
/ # ls
bin                fit-backup-volume  media              proc               sbin               tmp                var
dev                home               mnt                root               srv                to
etc                lib                opt                run                sys                usr

/ # cd to/
/to # ls
PG_VERSION            pg_dynshmem           pg_multixact          pg_snapshots          pg_tblspc             postgresql.auto.conf
base                  pg_hba.conf           pg_notify             pg_stat               pg_twophase           postgresql.conf
global                pg_ident.conf         pg_replslot           pg_stat_tmp           pg_wal                postmaster.opts
pg_commit_ts          pg_logical            pg_serial             pg_subtrans           pg_xact

/to # cd ..
/ # ls
bin                fit-backup-volume  media              proc               sbin               tmp                var
dev                home               mnt                root               srv                to
etc                lib                opt                run                sys                usr


/ # cd ..
/ # ls
bin                fit-backup-volume  media              proc               sbin               tmp                var
dev                home               mnt                root               srv                to
etc                lib                opt                run                sys                usr


/ # cd fit-backup-volume/
/fit-backup-volume # ls
/fit-backup-volume # cd ..


/ # cp -a to/. fit-backup-volume/
/ # cd fit-backup-volume/
/fit-backup-volume # ls
PG_VERSION            pg_dynshmem           pg_multixact          pg_snapshots          pg_tblspc             postgresql.auto.conf
base                  pg_hba.conf           pg_notify             pg_stat               pg_twophase           postgresql.conf
global                pg_ident.conf         pg_replslot           pg_stat_tmp           pg_wal                postmaster.opts
pg_commit_ts          pg_logical            pg_serial             pg_subtrans           pg_xact

/fit-backup-volume # exit
docker@docker:~/Fitbuddy_backup$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```
{% endcode %}

</details>

<details>

<summary>21th Nov</summary>

Python function in \`aicommitter\` to display the docs for common command and "How to start"

```python
def getReadme(filepath: str= "README_POST_INSTALL.md") -> str:
    try:
        with open(filepath, "r", "") as f:
            return f.read()
    except FileNotFoundError:
        return typer.style(
            f"Error: Documentation file '{filepath}' not found in the current directory.",
            fg=typer.colors.REDs,
        )
    except Exception as e:
        return typer.style(
            f"Error reading documentation file: {e}", fg=typer.colors.RED
        )
```

Run the python function when the user runs the aicommitter with a particular flag ( aicommitter docs )

```python
@app.command(name= "docs")
def show_docs():
    readme_content = getReadme()
    typer.echo(readme_content)
```







</details>

<details>

<summary>23rd Nov</summary>

### Monorepo structure for application

**In a monorepo, unrelated changes can make Docker do unnecessary work when deploying your app.**

\
So, to handle this, we use tool like turborepo that helps to separate out the dependency of the application.&#x20;

eg, the frontend application willl not trigger the build process of backend application, when any particular change is made to frontend `package.json`

Let's imagine you have a monorepo that looks like this:

![](<../.gitbook/assets/image (10).png>)

```yaml
.
├── README.md
├── WARP.md
├── apps
│   ├── api ( workspace 1 )
│   │   ├── Dockerfile
│   │   ├── README.md
│   │   ├── nest-cli.json
│   │   ├── package.json
│   │   ├── src
│   │   └── tsconfig.json
│   └── web ( workspace 2 )
│       ├── Dockerfile
│       ├── index.html
│       ├── package.json
│       ├── src
│       ├── tsconfig.json
│       ├── tsconfig.node.json
│       └── vite.config.ts
├── docker-compose.yml
├── package-lock.json
├── package.json
├── packages
│   └── domain
│       ├── package.json
│       ├── src
│       └── tsconfig.json
└── turbo.json
```

You want to deploy `apps/api` using Docker, so you create a Dockerfile:

```docker
FROM node:16
 
WORKDIR /usr/src/app
 
# Copy root package.json and lockfile
COPY package.json ./
COPY package-lock.json ./
 
# Copy the api package.json
COPY apps/api/package.json ./apps/api/package.json
 
RUN npm install
 
# Copy app source
COPY . .
 
EXPOSE 8080
 
CMD [ "node", "apps/api/server.js" ]
```

This will copy the root `package.json` and the root lockfile to the Docker image. Then, it'll install dependencies, copy the app source and start the app.

### Lockfile changes too often to trigger multiple build

{% hint style="warning" %}
**Installing a package inside `apps/api` should NOT change the root `package.json`**, _unless you are using a workspace setup that intentionally links them_.\
But the **root `package-lock.json`** _**will**_**&#x20;change**, because npm workspaces keep a single lockfile.
{% endhint %}

## Why does the lockfile change globally?

Because npm workspaces dedupe dependencies across the repo.

Example:

If `apps/api` installs `axios@1.7.2` and `apps/web` installs `axios@1.7.1`, npm will:

* try to place one version at the root-level `node_modules`
* resolve workspace dependency graph globally
* store EVERYTHING in a single lockfile

So even small workspace installs rewrite the global lockfil

## “If the root package-lock.json changes, do both apps rebuild?”

**Yes, in most CI/CD setups.**\
Because most pipelines check:

```
ON CHANGE of:
- apps/api/**
- apps/web/**
- package.json
- package-lock.json
```

And since `package-lock.json` is **shared**, a change in `apps/api` can trigger:

* rebuilding `apps/api` (expected)
* rebuilding `apps/web` (unexpected but normal for monorepos)

This is exactly why monorepos with **a single lockfile** have this drawback. To tackle this, we have turbo setup with us

If workspace B package.json changes → workspace A DOES NOT rebuild

#### If workspace A package.json changes → workspace B DOES NOT rebuild

#### ✔ Each workspace only tracks its OWN `inputs`.

#### Why this works

Turbo resolves `inputs` relative to the workspace where the command runs:

```
apps/api/build → inputs resolve inside apps/api
apps/web/build → inputs resolve inside apps/web
```

Nothing outside that directory is considered — unless you explicitly put:

```
"inputs": ["../../package.json"]
```

…which you didn’t.

So everything stays nicely isolated.

\
If I install a package inside API, does root package.json change?

**No. Only `apps/api/package.json` changes.**

#### If the root package.json changes, do both apps rebuild?

**Usually yes**, unless Turbo pipeline ignores it.

#### Does installing inside API modify root package.json?

**Never.**\
But it **always modifies root package-lock.json**.

### Turbo Repo setup

[https://turborepo.com/docs/guides/tools/docker#the-lockfile-changes-too-often](https://turborepo.com/docs/guides/tools/docker#the-lockfile-changes-too-often)

## Workspace in Turbo

Each directory ( api or web ) in repository means a workspace for turbo. So, `api`  is a workspace. By default, turbo stores a package.json file at workspace level

```json
{
  "pipeline": {
    "build": {
      "inputs": ["src/**", "package.json"], 
      "outputs": ["dist/**"]
    }
  }
}
```

## How Turbo resolves file paths

Turbo resolves `inputs` paths **relative to the workspace directory**, not the root of the repo.

So for:

```
apps/api
├── src
├── package.json
```

Turbo sees:

```
apps/api/src/**
apps/api/package.json
```

Root files are irrelevant unless referenced with `../..`&#x20;

{% hint style="warning" %}
"inputs": \["src/\*\*", "package.json", "../../package.json"]
{% endhint %}

#### **Turbo Default Inputs**

* All files in the workspace\
  `**/*`
* Root-level package-lock.json
* Root-level package.json
* Workspace-level package.json
* tsconfig.json
* .env files
* Basically everything except ignored stuff
* _AND_ files in dependent workspaces

### Containerisation

\
**Use `turbo prune` to send only relevant code to Docker**

This is the #1 best practice for monorepos.

Example (for api):

```
turbo prune --scope=api --docker
```

Outputs:

```
./out/
  json/
  full/
```

Docker builds using the **pruned workspace**, meaning:

* only api
* only its direct dependencies
* NOT web
* NOT unrelated packages
* NOT entire monorepo

This speeds up builds by 10–20x.

***

### **2. Use Multi-Stage Docker Builds with Turbo Cache Layers**

```docker
# -----------------------
# 1. Base Turbo layer
# -----------------------
FROM node:18 AS base
WORKDIR /app

# Install only root deps for pruning
COPY . .
RUN npm install -g turbo

# -----------------------
# 2. Prune the workspace
# -----------------------
FROM base AS pruned
RUN turbo prune --scope=api --docker

# -----------------------
# 3. Install dependencies
# -----------------------
FROM node:18 AS deps
WORKDIR /app
COPY --from=pruned /app/out/json/ .
RUN npm ci

# -----------------------
# 4. Build app
# -----------------------
FROM node:18 AS builder
WORKDIR /app
COPY --from=pruned /app/out/full/ .
COPY --from=deps /app/node_modules ./node_modules
RUN npm run build

# -----------------------
# 5. Final runtime image
# -----------------------
FROM node:18-slim AS runner
WORKDIR /app
COPY --from=builder /app/dist ./dist
CMD ["node", "dist/main.js"]

```





</details>

<details>

<summary>24th Nov</summary>

## **Use&#x20;**_**docker logs**_**&#x20;but fancy**

#### Install **Stern**

```bash
brew install stern
```

Then:

```bash
stern .
```

Streams logs from all running containers. No UI though.

### Using lazydocker for GUI

[https://github.com/jesseduffield/lazydocker](https://github.com/jesseduffield/lazydocker)

Lazydocker provide a GUI for checking what containers are running, clubbing containers based on project, created using `docker-compose.yml`

**Installation**

```
brew install lazydocker
```

For using this often, add an alias in your local shell

**For mac**

```
echo "alias lzd='lazydocker'" >> ~/.zshrc
```

### Enable support for multi environment variables&#x20;

What is way to get a particular variable from a variable ( named environment\_variables ) that holds variables using different name ( like key:pari ) and i can get the value using

var.environment\_variables\["DEPLOY\_ENV"]

```terraform
variable "environment_variables" {
  type = map(string)
  default = {
    DEPLOY_ENV   = "prod"
    REGION       = "us-east-1"
    PARI         = "value123"
  }
}
```

Try accessing variable like below:

```terraform
var.environment_variables["DEPLOY_ENV"]
```

or, using dot notation (only if the key is a valid identifier):

```terraform
var.environment_variables.DEPLOY_ENV
```

</details>

<details>

<summary>25th Nov</summary>

#### Is Nginx needed?

Short Answer: Yes, absolutely.

Why? Your React application is just a collection of static files (HTML/JS). It has no "brain" or "server" of its own to listen for incoming requests.

* Node.js/NestJS (API): Can listen to ports and serve requests.
* React/Vite (Web): Cannot listen to ports on its own after it is built.

You need a web server to say, "Hi, I am listening on port 80. If anyone asks for the website, I will give them this `index.html` file." Nginx is the industry standard for this because it is incredibly fast and lightweight.

#### 1. The Problem: "Real" Files vs. "Fake" Routes

Imagine your Nginx container is a file cabinet. Inside that cabinet, after you build your project, you have exactly these files:

* `index.html`
* `style.css`
* `main.js`

Scenario A: Clicking a Link (Client-Side Routing)

1. You land on `localhost`. Nginx gives you `index.html`.
2. React loads in your browser.
3. You click a button that says "Go to Dashboard".
4. Crucial Part: The browser does not talk to the server. React simply erases the "Home" component from the screen and draws the "Dashboard" component. It also changes the URL bar to `/dashboard` to be helpful.
5. Result: Everything works. Nginx didn't even know you changed pages.

Scenario B: The Refresh (Server-Side Routing)

1. You are looking at `localhost/dashboard`.
2. You hit Refresh.
3. The browser forgets everything React was doing. It makes a brand new HTTP request to Nginx: _"Hey, give me the file named dashboard inside the folder /."_
4. Nginx looks in the file cabinet.
   * Is there a file named `dashboard`? No.
   * Is there a folder named `dashboard/index.html`? No.
5. Result: Nginx follows standard protocol and throws a 404 Not Found error.

***

#### 2. The Solution: The Nginx "Catch-All"

We need to teach Nginx to stop being so literal. We need to tell it: _"If you can't find the file the user asked for, don't panic. Just give them the index.html file anyway."_

Once the browser receives `index.html`, React loads up again, looks at the URL (`/dashboard`), and says, "Oh, the user is at /dashboard! I should render the Dashboard component."

**The Magic Command**

This logic is handled by this single line in `nginx.conf`:

Nginx

```
try_files $uri $uri/ /index.html;
```

Here is the step-by-step translation of what Nginx does with this command when you request `/dashboard`:

1. `$uri`: "Does a file named `dashboard` exist?"
   * _Nginx Checks:_ No.
   * _Action:_ Move to next step.
2. `$uri/`: "Does a directory named `dashboard/` exist?"
   * _Nginx Checks:_ No.
   * _Action:_ Move to next step.
3. `/index.html`: "Okay, I give up. Just serve `/index.html`."
   * _Nginx Checks:_ Yes, that file exists!
   * _Action:_ Send `index.html` to the browser with a "200 OK" status.

#### Summary

* The Problem: The URL `/dashboard` is a "virtual" location that only exists inside React's memory. It does not exist as a file on the server's hard drive.
* The Solution: We configure Nginx to serve the entry point (`index.html`) for _every_ unknown request, allowing React to take over and handle the routing inside the browser.

**CLIENT SIDE VS SERVER SIDE ( role of nginx )**&#x20;









</details>

<details>

<summary>27th Nov</summary>

Execution role (`ecsTaskExecutionRole`) is only for:

* Pulling images from ECR
* Sending logs to CloudWatch

\
It cannot access application secrets.

Your app uses **Task Role**, not Execution Role.

### **Squash Merge (Clean & Nice for Long-Running Branches)**

This makes your 150 commits appear as **one single commit** in `main`.

```bash
git checkout main
git pull origin main
git merge --squash your-branch
git commit -m "Your feature summary"
git push origin main
```

Good when:

* The branch spammed unnecessary commits like “fix typo”, “lol ok now it works”.
* You want a clean main branch.



## P41Pulse Monorepo

```
[![Deploy P41Pulse Infra](https://github.com/Particle41/p41-hack-team-1/actions/workflows/deployment.yml/badge.svg)](https://github.com/Particle41/p41-hack-team-1/actions/workflows/deployment.yml)
```

A Turborepo monorepo with NestJS backend and React frontend.

</details>

<details>

<summary>5th Dec</summary>

## Changelog

```md
# Changelog

All notable changes to this project will be documented in this file.

## [Unreleased]
### Added
- New user authentication system

## [1.2.0] - 2025-12-05
### Added
- Dark mode support
- Export to PDF functionality

### Fixed
- Bug where images wouldn't load on mobile

## [1.1.0] - 2025-11-15
### Changed
- Improved search performance
- Updated dependencies
```

</details>

<details>

<summary>7th Dec</summary>

```
grep -E "README|pyproject"
```

</details>





