---
description: Docker concepts and basic commands
---

# Docker

Commands:

```
// Some code
1. docker rmi -f d2c94e258dcb
2. docker images
3. docker pull <>
4. docker push
5. docker ps 
6. docker ps -a
7. docker container prune
8. docker exec -it <image> /bin/bash
9. docker run -d -p <port> <image>
10. docker run -d -p <port> -v <volume>:<volume> <image>
11. docker login <registry> --username <> --password <>

```

{% hint style="info" %}
\<VOL1>:\<VOL2>

\<VOL1> refer to the volume in the local machine

\<VOL2> refer to the volume inside the container
{% endhint %}
