# Sample Dockerfile

```docker
// Some code
# Use the official Python image from the Docker Hub
FROM python:3.9-slim

# Set the working directory
WORKDIR /usr/src/app

# Copy the requirements file into the container
COPY requirements.txt ./

# Install the dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the application into the container
COPY . .

# Expose port 5000 for the Flask app to run on
EXPOSE 5000

# Run the application
CMD ["python", "app.py"]


docker build -t flask-docker-app .

docker run -d -p 5000:5000 flask-docker-app
```

```docker
// Some code
FROM ubuntu
WORKDIR /usr/src/flaskapp
RUN apt-get update && apt-get install -y python3-pip
RUN apt-get install python3
COPY requirements.txt /usr/src/flaskapp
RUN pip3 install --break-system-packages Flask
#RUN pip3 install --no-cache-dir -r requirements.txt
COPY . /usr/src/flaskapp
EXPOSE 5000
ENTRYPOINT ["/usr/bin/python3"]
CMD ["app.py"]
```
