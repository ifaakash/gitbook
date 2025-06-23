# Day 1

Here’s a detailed plan for your 2-hour Docker learning session today:

#### **Today's Learning Plan (2 Hours)**

**1. Introduction to Docker (30 minutes)**

* **Read:**
  * Overview of Docker and its components (images, containers, Docker Hub).
  * Suggested resource: [Docker Documentation](https://docs.docker.com/get-started/) - "What is Docker?"
* **Watch:**
  * A quick introductory video (e.g., "What is Docker?" on YouTube).

**2. Installation (30 minutes)**

* **Install Docker on your MacBook:**
  * Follow the official [Docker installation guide](https://docs.docker.com/desktop/install/mac-install/).
* **Verify Installation:**
  *   Open your terminal and run:

      ```bash
      docker --version
      ```
  * Ensure you see the installed Docker version.

**3. Basic Docker Commands (1 hour)**

* **Run Your First Container (10 minutes):**
  *   In the terminal, run:

      ```bash
      docker run hello-world
      ```
  * This will confirm that Docker is installed correctly and running.
* **Explore Basic Commands (30 minutes):**
  * Experiment with the following commands and note their outputs:
    *   **List running containers:**

        ```bash
        docker ps
        ```
    *   **List all containers (including stopped ones):**

        ```bash
        docker ps -a
        ```
    *   **Stop a container:**

        ```bash
        docker stop <container_id>
        ```
    *   **Remove a container:**

        ```bash
        docker rm <container_id>
        ```
* **Optional Challenge (20 minutes):**
  *   Create a simple Dockerfile for a basic Python application. Here’s an example to get you started:

      ```Dockerfile
      # Use the official Python image from Docker Hub
      FROM python:3.9

      # Set the working directory
      WORKDIR /app

      # Copy the current directory contents into the container
      COPY . /app

      # Install any needed packages specified in requirements.txt
      RUN pip install --no-cache-dir -r requirements.txt

      # Expose port
      EXPOSE 80

      # Command to run the application
      CMD ["python", "app.py"]
      ```

**4. Wrap Up (10 minutes)**

* **Review:**
  * Reflect on what you've learned today. Make a note of any questions or concepts you'd like to explore further.
* **Plan for Tomorrow:**
  * Think about continuing with Docker Compose or a deeper dive into containers.

Feel free to adjust the timing or tasks based on your pace. Good luck, and enjoy your learning! If you have any questions during your session, just let me know!
