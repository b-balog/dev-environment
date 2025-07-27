# 1. Docker basics

Docker is an open platform for developers. 
Its main purpose is to help developing, shipping and running applications, platform independently.

In this section, we will get a brief non-exhaustive understanding of docker  
and its viability in a developer environment. For extensive basics, [here](https://docs.docker.com/get-started/docker-overview/) is the original docker documentation.

_SIDE NOTE: If you are not familiar with `venv` (Virtual Environment), please read [this](https://docs.python.org/3/library/venv.html) python website before going further._

## 1.1 The Docker daemon

The Docker daemon (`dockerd`) listens for Docker API requests and manages Docker objects such as images, 
containers, networks, and volumes. A daemon can also communicate with other daemons to manage Docker services.

## 1.2 The Docker client

The Docker client (`docker`) is the primary way that many Docker users interact with Docker. 
When you use commands such as `docker run`, the client sends these commands to `dockerd`, 
which carries them out. The docker command uses the Docker API. 
The Docker client can communicate with more than one daemon.

## 1.3 Docker registries

A Docker registry stores Docker images.
Docker Hub is a public registry that anyone can use, and Docker looks for images on Docker Hub by default. 
You can even run your own private registry.

When you use the `docker pull` or `docker run` commands, Docker pulls the required images from your configured registry. 
When you use the `docker push` command, Docker pushes your image to your configured registry.

## 1.4 Docker objects

When you use Docker, you are creating and using images, containers, networks, volumes and other objects. 
This section is a brief overview of some of those objects.

### 1.4.1 The Docker image

An image is a read-only template with instructions for creating a [Docker container](#142-the-docker-container). 
Often, an image is based on another image, with some additional customization. 
For example, you may build an image which is based on the [`python:3.10-slim`](https://hub.docker.com/layers/library/python/3.10-slim/images/sha256-7bc78a41cf5a9069b45b71091ccbc4d0e1b1e009a89e07854483cb9ebf4a1c56), 
which is a minimal (slim) image containing only the essentials needed to run Pyton 3.10. 
Your image may install other packages, software, as well as the configuration details 
needed to make your application run. (_The `python:3.10-slim` is also based on other images._) 

You might create your own images, or you might only use those created by others and published in a registry. 
To build your own image, you create a Dockerfile with a simple syntax for defining the steps needed to create the image and run it. 
Each instruction in a Dockerfile creates a layer in the image. 
When you change the Dockerfile and rebuild the image, only those layers, which have changed, are rebuilt. 
This is part of what makes images so lightweight, small, and fast, when compared to other virtualization technologies.

#### EXAMPLE:

```dockerfile
# Defining the parent image(s) -> -> This is a necessity!!
FROM python:3.10-slim

# Installing and update linux packages -> These sometimes are application specific packages -> Not a necessity, but recommended
RUN apt-get update && apt-get install -y \
    sudo \
    && rm -rf /var/lib/apt/lists/* 

# Setting the working directory to /app -> This is a necessity!!
# (you can set the working directory to whatever you want, but /app is a convention in production images)
WORKDIR /app

# Updating pip -> Not a necessity, but recommended
RUN python -m ensurepip && pip install --upgrade pip
```

##### Basic Docker syntaxes ([for more](https://docs.docker.com/reference/dockerfile/))

***

`FROM` -> Defines the parent image or images

```dockerfile
FROM python:3.10-slim
```

***

`RUN` -> runs a bash command
```dockerfile
RUN apt-get update
```
```dockerfile
RUN ["apt-get", "update"]
```

***

`CMD` -> Executes the specified command only when running the container
```dockerfile
CMD apt-get update
```
```dockerfile
CMD ["apt-get", "update"]
```

***

`COPY` -> To copy files and folders into the image

If you copy files or directories into the image, they won't be accessible from outside the running container, 
and they increase the size of the built image!!

The last argument is always the destination inside the image.
Before that, the desired files and folders you want to copy into the image.

```dockerfile
COPY file1.txt file2.txt /usr/src/things/
```
```dockerfile
COPY ["file1.txt", "file2.txt", "/usr/src/things/"]
```

If you want to copy a folder:
```dockerfile
COPY /desired/folder /destination
```

If you want to copy only the files contained by a folder:

```dockerfile
COPY /desired/folder/ /destination
```

***

`ENTRYPOINT` -> Works similarly to `RUN` and `CMD`. 
This specifies the command to run when starting the container. 
This allows you to configure a container to run as an executable.

***



### 1.4.2 The Docker container



