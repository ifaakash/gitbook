---
description: >-
  This page contain details on how to create a backup of docker volume in local
  ( Host )
icon: docker
---

# Creating backup of a Docker volume in local

```docker
docker run -it --rm -v $(pwd)/backup:/fit-backup-volume -v fitbuddy_fitbuddy_db_volume:/fit-backup-volume alpine sh
docker run -it --rm -v $(pwd)/backup:/fit-backup-volume -v fitbuddy_fitbuddy_db_volume:/to alpine sh
```

#### Your command

```bash
docker run -it --rm -v $(pwd)/backup:fitbuddy_fitbuddy_db_volume alpine sh
```

***

#### What’s wrong here (the subtle bit)

The syntax for `-v` (volume mount) can represent **two completely different things**, depending on whether Docker thinks the right-hand side is:

1. a **path** on the host
2. or a **Docker-managed volume name**

***

#### Docker’s Mount Resolution Logic

Docker parses:

```bash
-v SOURCE:TARGET
```

and decides:

* <mark style="background-color:yellow;">if</mark> <mark style="background-color:yellow;"></mark><mark style="background-color:yellow;">`SOURCE`</mark> <mark style="background-color:yellow;">**starts with a**</mark><mark style="background-color:yellow;">**&#x20;**</mark><mark style="background-color:yellow;">**`/`**</mark><mark style="background-color:yellow;">, it’s treated as a</mark> <mark style="background-color:yellow;"></mark><mark style="background-color:yellow;">**host path**</mark> <mark style="background-color:yellow;"></mark><mark style="background-color:yellow;">(bind mount).</mark>
* <mark style="background-color:yellow;">if</mark> <mark style="background-color:yellow;"></mark><mark style="background-color:yellow;">`SOURCE`</mark> <mark style="background-color:yellow;">**does NOT start with a**</mark><mark style="background-color:yellow;">**&#x20;**</mark><mark style="background-color:yellow;">**`/`**</mark><mark style="background-color:yellow;">, it’s treated as a</mark> <mark style="background-color:yellow;"></mark><mark style="background-color:yellow;">**Docker volume name**</mark><mark style="background-color:yellow;">.</mark>

So your command:

```bash
-v $(pwd)/backup:fitbuddy_fitbuddy_db_volume
```

means:

* `SOURCE = $(pwd)/backup` → host directory&#x20;
* `TARGET = fitbuddy_fitbuddy_db_volume` → **a path inside the container,** not a volume reference

That means inside the container, you’ll just get a folder called `/fitbuddy_fitbuddy_db_volume` containing your local `backup` contents — **it won’t connect to the actual Docker volume at all**.

***

#### Correct syntax (to mount a volume _and_ a host path)

If you want to **mount the Docker volume** and **your host path** at the same time, you must use two `-v` flags:

```bash
docker run -it --rm \
  -v fitbuddy_fitbuddy_db_volume:/from \
  -v $(pwd)/backup:/to \
  alpine sh
```

Now inside the container:

* `/from` → the **real Docker volume**
* `/to` → your **host backup folder**

Then from inside the container:

```bash
cp -a /from/. /to/
```

***

#### Think of it like this

| Concept        | Mount Syntax                       | Inside Container                    |
| -------------- | ---------------------------------- | ----------------------------------- |
| Host directory | `/path/on/host:/path/in/container` | Gives access to host data           |
| Docker volume  | `volume_name:/path/in/container`   | Gives access to Docker-managed data |

You can use _both_ together, but not interchange them in one mount.

***

#### TL;DR

| Your Command                                                | What Happens                                                                                                   |
| ----------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `-v $(pwd)/backup:fitbuddy_fitbuddy_db_volume`              | Mounts local folder to a container path literally named `/fitbuddy_fitbuddy_db_volume` (not the Docker volume) |
| `-v fitbuddy_fitbuddy_db_volume:/from -v $(pwd)/backup:/to` | Mounts both correctly                                                                                          |
