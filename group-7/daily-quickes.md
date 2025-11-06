---
description: This page include all the quickies done on a particular day
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

