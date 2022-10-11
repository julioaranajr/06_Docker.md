# 06_Docker.md
Hand on Labs [Introduction to Containers]

> **Tasks**:
> - Task 0: Prerequisites
> - Task 1: Run simple containers
> - Task 2: Package your custom application using Docker
> - Task 3: Running your application on Kubernetes locally

## Task 0: Prerequisites

First, you need to install Docker.

### Install Docker Desktop on Mac

Docker Desktop is an easy-to-install application for your Mac, Linux, or Windows environment 
that enables you to build and share containerized applications and microservices.

It provides a simple interface that enables you to manage your containers, applications, and 
images directly from your machine without having to use the CLI to perform core actions.

What's included in Docker Desktop?
- What are the key features of Docker Desktop?
       - Docker Engine
       - Docker CLI client
       - Docker Buildx
       - Docker Compose
       - Docker Content Trust
       - Kubernetes
       - Credential Helper

Docker Desktop works with your choice of development tools and languages and gives you access 
to a vast library of certified images and templates in Docker Hub. This enables development teams 
to extend their environment to rapidly auto-build, continuously integrate, and collaborate using 
a secure repository.

[Docker Documentation](https://docs.docker.com/desktop/mac/install/)

Then launch the Docker app and confirm your password for privileged access. (Might need a restart)
```bash
# Run Docker Desktop Application
open /Applications/Docker.app
```

### Run a simple nginx container

1. Check if Docker was installed successfully:

```bash
# Chech your Version
docker version
```

2. Run the following command:

```bash
# Command to pull a image of nginx from Docker Hub
docker run -d -p 80:80 --name webserver nginx
```

When `nginx:latest` image could not be found locally, Docker automatically _pulls_ it form Docker Hub.

You’ll notice a few flags being used. Here’s some more info on them:

- -d - run the container in detached mode (in the background)
- -p 80:80 - map port 80 of the host to port 80 in the container
- nginx - the image to use

3. Try to access a container on localhost:80 on your machine: http://127.0.0.1:80/.

4. Check how long it will take to start a new container with a new image:

```bash
# Run this command to create a new container
docker run -d -p 8080:80 --name webserver2 nginx
```

The image was downloaded already, so this container started faster.

5. You can run bash commands inside a Linux container:

```bash
# This command open an interactive console in the container
docker exec -it webserver /bin/bash
```

docker exec runs a command in a running container and the switch -it opens an interactive console

6. Stop containers and remove the image:

```bash
# command to STOP containers before remove it
docker stop webserver
docker stop webserver2
# command to REMOVE containers
docker rm webserver
docker rm webserver2
# command to REMOVE IMAGES in Docker Hub
docker rmi webserver
docker rmi webserver2
```

## <a name="Task_2"></a>Task 2: Package your custom application using Docker

You can create an image for your application by using Dockerfile. It contains a list of instructions 
to build images such as installing a package, downloading source code, using a base image.

1. Create a new folder:

```bash
# create the directory
mkdir web-image
# access to the directory
cd web-image
```
Create a file for the example website:
```bash
code index.html
```
```html
<html>
<body>
<h1>Hello world</h1>
<p>This is a Talent-Academy Exercise</p>
</body>
</html>
EOF
```

3. Create a Dockerfile and add the following:

**Dockerfile** is a text file that contains a list of instructions needed to build a given image. For example: install a package, download source code, use a base image.

```dockerfile
FROM ubuntu
RUN apt-get update
RUN apt-get install nginx -y
COPY index.html /var/www/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

- [FROM](https://docs.docker.com/engine/reference/builder/#from) specifies the base image to use as the starting point for this new image you're creating.
- [RUN](https://docs.docker.com/engine/reference/builder/#run) Executes a command on top of an existing layer and creates a new resulting layer
- [COPY](https://docs.docker.com/engine/reference/builder/#copy) Copies files and directories from a source (host executing build command) to a destination (Container image)

4. Build Image

Run `docker image build` command to create a new Docker image using the instructions in your Dockerfile.

- `--tag` allows us to give the image a custom name. It usually consists of your Docker Hub username, the application name, and a version.
- `.` tells Docker to use the current directory as the build context

:warning: :warning: Be sure to include the period (`.`) at the end of the command. :warning: :warning:

```bash
docker build -t <image-name> --tag <yourusername>/<image-name>:1.0 .
```

5. (Optional) Push your image to Docker Hub

```bash
# List local images:
docker images

# Log in to Docker Hub:
docker login --username <yourusername>

# Push the image to your repository in Docker Hub:
docker push <yourusername>/<image-name>:1.0
```