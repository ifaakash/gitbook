# Docker exectuion commands

{% hint style="info" %}
docker volume create my\_volume

docker run -d -v my\_volume:/data --name my\_data\_container busybox sh -c "echo 'Hello, World!' > /data/hello.txt"

docker run --rm -v my\_volume:/data busybox cat /data/hello.txt

docker run -d -v /path/to/local/dir:/data --name my\_bind\_mount busybox sh -c "echo 'Hello from bind mount!' > /data/hello.txt"

cat /path/to/local/dir/hello.txt
{% endhint %}

COPY .

* The first . (period) represents the source, which is the current directory on the host machine where the Dockerfile is located.
* The second . (period) represents the destination, which is the current working directory inside the container (set by the WORKDIR /app instruction).
* So, this instruction copies all the files and directories from the current directory on the host machine into the current working directory (/app) inside the container.

COPY requirements.txt requirements.txt



* The first requirements.txt is the source file, which is the requirements.txt file located in the current directory on the host machine.
* The second requirements.txt is the destination file, which is also named requirements.txt but located in the current working directory (/app) inside the container.
* The reason for using the same name for the source and destination is that you want to preserve the original file name when copying it into the container.

In the case of COPY requirements.txt requirements.txt, you could have used a different destination file name, like COPY requirements.txt /app/requirements.txt. However, using the same name makes it more readable and easier to understand that you're copying the requirements.txt file into the container while preserving its original name.

Here's a breakdown of the two COPY instructions:

COPY . .



* Source: The entire current directory on the host machine
* Destination: The current working directory (/app) inside the container

COPY requirements.txt requirements.txt



* Source: The requirements.txt file in the current directory on the host machine
* Destination: The requirements.txt file in the current working directory (/app) inside the container

By using these two COPY instructions, the Dockerfile ensures that the application code and the requirements.txt file are both copied into the container, allowing the application to be built and run inside the container environment.
