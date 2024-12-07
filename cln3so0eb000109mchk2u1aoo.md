---
title: "Essential Docker Commands"
datePublished: Thu Sep 28 2023 23:16:27 GMT+0000 (Coordinated Universal Time)
cuid: cln3so0eb000109mchk2u1aoo
slug: essential-docker-commands
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/BFlD8qNL2UI/upload/9acfb1c977a026b7907ca90d6bd1c1dc.jpeg
tags: linux, docker, devops, containers

---

In this blog post, I wanted to share a high-level overview of Docker and list some of its basic CLI commands and their usage.

## What is Docker?

Docker is an open-source platform that automates the deployment of applications inside lightweight, portable containers. Containers are isolated environments that package an application and its dependencies, ensuring consistency across different environments.

## List of Useful Docker Commands:

### 1\. **docker pull**

* **Usage**: `docker pull <image_name>`
    
* **Description**: This command fetches a Docker image from a registry like Docker Hub. Images serve as templates for containers, making this the initial step to container creation.
    
* **Example**: `docker pull ubuntu:latest`
    

### 2\. **docker run**

* **Usage**: `docker run [options] <image_name>`
    
* **Description**: The **docker run** command is fundamental to creating and running containers. It allows you to customize various aspects of the container's behavior using options. Below are some commonly used options:
    
    * `-d, --detach`: Run the container in the background, detaching it from your terminal.
        
    * `--name <container_name>`: Assign a custom name to the container, making it easier to manage.
        
    * `-p <host_port>:<container_port>`: Map a port on your host machine to a port inside the container, enabling network communication.
        
    * `-v <host_path>:<container_path>`: Create a volume by mapping a directory or file on your host to a directory inside the container, allowing data to persist.
        
    * `--env <key=value>`: Set environment variables within the container.
        
    * `--network <network_name>`: Attach the container to a specific network, facilitating communication with other containers.
        
    * `--restart <policy>`: Define the container's restart policy (e.g., "no", "always", "on-failure").
        
    * `--memory <limit>`: Set a memory limit for the container.
        
    * `--cpu-shares <shares>`: Specify the CPU shares allocated to the container.
        
    * `--privileged`: Give the container full access to the host's devices, which can be useful for certain tasks but should be used with caution.
        
* **Example**:
    
    * Start a detached container named "web\_app" from the "my\_web\_app\_image" image, mapping host port 8080 to container port 80, and creating a volume to persist data:
        
        ```plaintext
        docker run -d --name web_app -p 8080:80 -v /host/data:/container/data my_web_app_image
        ```
        
    * Launch a container in the "my\_network" network with a custom environment variable and a memory limit:
        
        ```plaintext
        docker run --network my_network --env MY_VARIABLE=123 --memory 512m my_image
        ```
        

### 3\. **docker ps**

* **Usage**: `docker ps [options]`
    
* **Description**: Listing all currently running containers is a breeze with this command. Add the `-a` option to view all containers, including those that have stopped.
    
* **Example**: `docker ps -a`
    

### 4\. **docker exec**

* **Usage**: `docker exec [options] <container_name> <command>`
    
* **Description**: To execute a command within a running container, use this command. It comes in handy for running additional processes or accessing the container's shell.
    
* **Example**: `docker exec -it my_container bash`
    

### 5\. **docker stop**

* **Usage**: `docker stop <container_name>`
    
* **Description**: This command gracefully stops a running container by sending a termination signal to its main process.
    
* **Example**: `docker stop my_container`
    

### 6\. **docker rm**

* **Usage**: `docker rm <container_name>`
    
* **Description**: Remove stopped containers with this command. Use the `-f` option to forcefully remove a running container.
    
* **Example**: `docker rm my_container`
    

### 7\. **docker rmi**

* **Usage**: `docker rmi <image_name>`
    
* **Description**: Delete a Docker image using this command. Ensure no containers are using the image before attempting removal.
    
* **Example**: `docker rmi ubuntu:latest`
    

### 8\. **docker build**

* **Usage**: `docker build [options] -t <image_name> <path_to_Dockerfile>`
    
* **Description**: The **docker build** command is used to build a Docker image from a Dockerfile. It provides several options to fine-tune the build process. Below are some commonly used options:
    
    * `-t, --tag <name:tag>`: Assign a name and optional tag to the built image for easier identification.
        
    * `--file <path/to/Dockerfile>`: Specify the path to the Dockerfile if it's not named "Dockerfile" or located in a different directory.
        
    * `--build-arg <key=value>`: Pass build-time variables to the Dockerfile.
        
    * `--no-cache`: Build the image without using the cache, ensuring a fresh build.
        
    * `--pull`: Always attempt to pull a newer version of the base image.
        
    * `--squash`: Optimize the resulting image by squashing the layers into a single layer.
        
    * `--target <stage>`: Build up to a specific build stage in a multi-stage Dockerfile.
        
    * `--buildkit`: Enable BuildKit, a modern build subsystem for Docker, to take advantage of advanced features and improved performance.
        
* **Example**:
    
    * Build an image named "my\_custom\_image" from the Dockerfile in the current directory and set a build-time variable:
        
        ```plaintext
        docker build -t my_custom_image --build-arg MY_VARIABLE=123 .
        ```
        
    * Build a Dockerfile located in a different directory and name the resulting image:
        
        ```plaintext
        docker build -t my_other_image --file /path/to/Dockerfile/ my_custom_directory
        ```
        

### 9\. **docker-compose**

* **Usage**: `docker-compose [options] <command>`
    
* **Description**: Manage multi-container Docker applications defined in a YAML file (docker-compose.yml) using this command. It's indispensable for orchestrating complex applications with multiple services.
    
* **Example**: `docker-compose up`
    

### 10\. **docker logs**

* **Usage**: `docker logs [options] <container_name>`
    
* **Description**: Display the logs of a running container with this command. It's a valuable tool for debugging and monitoring containerized applications.
    
* **Example**: `docker logs my_container`
    

### **11\. docker stop all running containers at once (combining commands)**

* **Usage**: `docker stop $(docker ps -q)`
    
* **Description**: To stop all running containers at once, use this command. It leverages a subcommand to obtain the list of running container IDs and passes them to the `docker stop` command.
    
* **Example**: `docker stop $(docker ps -q)`
    

I hope to share more of what I've learned in future blog posts.