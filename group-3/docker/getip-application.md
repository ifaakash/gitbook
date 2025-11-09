---
description: This page describe the codebase for `getIP` application
icon: docker
---

# getIP application

`getIP` application is used to get the current Timestamp when a user hits the homepage URL for the application.

## Repository Structure

```
tree -L 1
.
├── docker-compose.yml
├── Dockerfile
├── main.py
└── requirements.txt

1 directory, 4 files
```

## Codebase

### main.py

```python
from flask import Flask, jsonify
from datetime import datetime, timezone, timedelta

app = Flask(__name__)

# Adjust timezone if needed (e.g. +05:30 for IST)
IST = timezone(timedelta(hours=5, minutes=30))


@app.route("/")
def get_timestamp():
    current_time = datetime.now(IST).isoformat()
    return jsonify({"timestamp": current_time})


if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)

```

<mark style="background-color:yellow;">main.py declares the endpoint like</mark> **'/'** that is available to user, when he hits the URL for the application.&#x20;

{% hint style="danger" %}
'0.0.0.0' is used to declare what the application exposes. It cannot be 127.0.0.1, as this might lead to issue when exposing the application upon containerizing it.
{% endhint %}

#### Components of main.py

**app= Flask( \_\_name \_\_ )**

{% hint style="success" %}
This is used to declare the object of the class – Flask. Using app, we can use the method of the Flask class, like **routes**&#x20;
{% endhint %}

**@app.route('/')**

{% hint style="success" %}
Routes define the endpoints that are available to user, when he hits the application URL.&#x20;
{% endhint %}

### Dockerfile

Dockerfile list the steps to containerize any application. Below is the dockerfile for above <mark style="background-color:yellow;">flask</mark> application:

{% code lineNumbers="true" %}
```docker
FROM python:3.10-slim@sha256:975a1e200a16719060d391eea4ac66ee067d709cc22a32f4ca4737731eae36c0
WORKDIR /app
COPY requirements.txt ./
RUN pip install -r requirements.txt 
COPY . ./
EXPOSE 5000
CMD ["python", "main.py"]
```
{% endcode %}

Line 1 :

&#x20;Locks out the specific tag and the particular image to use. Even if the tag has different version of images in it, the SHA helps to lock out the image. This is a <mark style="background-color:yellow;">good practice in production</mark>, as it helps to **lock-out the application version**, and helps to resolve the dependencies support issue.&#x20;

Line 3 :

This line copy the requirements.txt file into the working directory of container. This list any required package, used to build the application. We first copy and then install these packages/dependencies, and then copy the rest of the application code to build the application.

Line 6 :&#x20;

We expose any particular port, on which the user will be able to hit the application URL:\<port>

This is important line to expose the application publicly, else the application remains private.

Line 7 :&#x20;

CMD is used to mention the command required to run the application. We have **ENTRYPOINT** and **CMD**

### Docker compose file

{% code lineNumbers="true" %}
```yaml
name: getIP
services: 
    root:
        build: .
        container_name: getIP-container
        ports:
         - "80:5000"
        networks:
          - getIP
 networks:
  - getIP
```
{% endcode %}

## Dockerhub

The above image is available in [Dockerhub](getip-application.md#repository-structure) as public image
