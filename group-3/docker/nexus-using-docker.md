---
description: >-
  Nexus can serve as a private Docker registry, which is particularly useful for
  storing and managing your Docker images.
---

# Nexus using Docker

##

**1. Nexus as a Docker Registry**

Nexus can serve as a private Docker registry, which is particularly useful for storing and managing your Docker images.

* **Set Up a Docker Hosted Repository**:
  1. Log into Nexus and create a new Docker (hosted) repository.
  2. Choose a port for the Docker registry (e.g., 5000).
*   **Push Docker Images to Nexus**:

    ```
    docker tag myapp:latest localhost:5000/myapp:latest
    docker push localhost:5000/myapp:latest
    ```
* You may need to authenticate with Nexus if it’s configured to require credentials.
*   **Pull Docker Images from Nexus**:

    ```
    docker pull localhost:5000/myapp:latest
    ```

{% hint style="info" %}
Path for password: /opt/sonatype/sonatype-work/nexus3

&#x20;

&#x20;

Push image to maven repo:

docker tag \<local\_image\_name>:\<tag> \<nexus\_host>:\<nexus\_port>/\<repository\_name>:\<tag>



Nexus URL: [http://localhost:8081/repository/aac-docker-regis/](http://localhost:8081/repository/aac-docker-regis/)&#x20;

docker tag hello-world:latest localhost:8082/repository/aac-docker

[http://localhost:8081/repository/aac-docker/](http://localhost:8081/repository/aac-docker/)

&#x20;

docker pull localhost:69/repository/aac-docker/hello-world:latest

docker tag myapp:latest localhost:5000/myapp:latest

docker push localhost:5000/myapp:latest

docker pull localhost:5000/myapp:latest
{% endhint %}

```
// kubectl create secret docker-registry nexus-secret \
  --docker-server=localhost:69 \
  --docker-username=admin \
  --docker-password=admin
```
