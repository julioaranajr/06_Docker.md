# Hands-On: Introduction to Containers (Docker and Minikube)
**Tasks:**

> - Task 0: Prerequisites
> - Task 1: Run simple containers
> - Task 2: Package your custom application using Docker
> - Task 3: Running your application on Kubernetes locally
> - Task 0: Prerequisites

# First, you need to install Docker.

> - Install Docker Desktop on Mac
> - [Docker Documentation](https://docs.docker.com/desktop/mac/install/)
```
Then launch the Docker app and confirm your password for privileged access. 
(Might need a restart)
```

# Run Docker Desktop
```
open /Applications/Docker.app
```
# Run a simple nginx container

1.- Check if Docker was installed successfully:
```
docker version
```
# Run the following command:
```
2.- docker run -d -p 80:80 --name webserver nginx
```
When nginx:latest image could not be found locally, Docker automatically 
pulls it form Docker Hub.

You’ll notice a few flags being used. Here’s some more info on them:
```
-d - run the container in detached mode (in the background)
-p 80:80 - map port 80 of the host to port 80 in the container
nginx - the image to use
```
3.- Try to access a container on localhost:80 on your**
machine: http://localhost:80/.

4.- Check how long it will take to start a new container with a new image:
```
docker run -d -p 8080:80 --name webserver2 nginx
```
The image was downloaded already, so this container started faster.

5.- You can run bash commands inside a Linux container:
```sh
docker exec -it webserver /bin/bash
```
docker exec runs a command in a running container and the switch -it opens an 
interactive console

6.- Stop containers and remove the image:
```sh
docker stop webserver
docker rm webserver
Docker rmi <image name>
```

# Task 2: Package your custom application using Docker

You can create an image for your application by using Dockerfile. It contains a list of 
instructions to build images such as installing a package, downloading source code, using a base image.

1.- Create a new folder:
```sh
mkdir web-image
cd web-image

# Create a file for the example website

cat <<EOF >>index.html
<html>
<body>
<h1>Hello world</h1>
<p>This is a Talent-Academy Exercise</p>
</body>
</html>
EOF
```

2.- Create a Dockerfile and add the following:**
**Dockerfile** is a text file that contains a list of instructions needed to build a 
given image. For example: install a package, download source code, use a base image.

```sh
FROM ubuntu
RUN apt-get update
RUN apt-get install nginx -y
COPY index.html /var/www/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

**FROM** specifies the base image to use as the starting point for this new image you're creating.
**RUN** Executes a command on top of an existing layer and creates a new resulting layer
**COPY** Copies files and directories from a source (host executing build command) to a destination (
Container image)

3.- Build Image
Run `docker image` build command to create a new Docker image using the instructions in your Dockerfile.

- `--tag` allows us to give the image a custom name. It usually consists of your Docker Hub username, 
the application name, and a version.
`.` tells Docker to use the current directory as the build context
**Be sure to include the period `(.)` at the end of the command.**

```sh
docker build -t <image-name> --tag <yourusername>/<image-name>:1.0 .
```

5.- (Optional) Push your image to Docker Hub
```sh
# List local images:
docker images

# Log in to Docker Hub:
docker login --username <yourusername>

# Push the image to your repository in Docker Hub:
docker push <yourusername>/<image-name>:1.0
```