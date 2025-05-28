# Docker Basics

  - Basic concept:
    + Container image <--> class
    + Container       <--> instance
  - Container vs. Virtualisation
    + Virtualisation: at hardware level --> create "virtual" hardware
    + Container: at the OS (kernel) level --> create virtual OS
    + Advantage: smaller footprint, faster start up, while ensure consistent deployment of production (by ensure all dependencies are included)
  - Microservices: split each application feature as its own small app, or microservices
    + Advantage: can scale up/down, patch/upgrade each microservices **separatedly** based on demand
  - Cloud-native microservices: microservices that makes use of cloud capabilities: self-healing, self-scale, auto-update, etc.
  - Docker services: allows multiple identical copy of containers running together as a service
    + automatic start new containers when one or more are killed
    + serve at the same port, also do load balancing
    
## Tools    
  - Software
    + Docker Desktop: good for local installation
    + Play with Docker: 4h-limit free playground for Docker Hub account, good to try & maybe learn
  - Tools to manage containers: Docker Swarm, Kubernetes
  - VS Code Docker Extension: add syntax highlighted, help, manage docker images, etc.

## Docker image
  - Dockerfile: a text document that contains all the commands a user could call on the comand line to assemble an image
    + Similar as a source file, which need building to produce the docker image
  - Dockerfile is arrange into layers: 
    + FROM: the base image to start FROM
    + LABL: metadata about the docker (i.e. author, creation date, etc.)
    + ENV: envinronment settings
    + WORKDIR: working directory where the container starting FROM
    + `COPY . .`: copy from <current> directory to container working directory
    + `RUN <command-to-run>`: run the <command-to-run>
    + EXPOSE: external port this container will be in contact with
    + ENTRYPOINT: which command to run when the container is started (e.g. `ENTRYPOINT ["node", "server.js"]`
  - Building docker: `docker build -t <image-name> -f <docker-file-name> <path-to-docker-file>`
  - Deploy an image to docker hub: `docker push <username>/<image-name>:<tag>`
    + Can use `docker pull` to download the image from docker hub
  - Container registry: the image name become `<registry-name>/<image-name>:<tag>`
    + `registry-name`: often is the username in the registry
    + `tag`: kind of like the version
    
## Container
  - Image: layer of modification (read-only) on top of a based image --> **Immutable**
  - Container: the last layer on top of the image, but can Read/Write --> **Mutable instance**
  - Basic commands: `docker run -n <container-name> -p <external-port>:<internal-port> <image-nane>`
    + Need the image locally before can create & run the container
    + often include option `-d`: run in detached mode, where container won't lock up the current console for IO
  - Viewing logs: `docker logs <container-id>`
    + Use `docker ps` to get id(s) of running container. 
      - `docker ps -a` for id(s) of all containers
  - Volumes: use to store data "outside" of the container --> persist data
    + Syntax: `-v <external-path>:<container-path>`
    + Example: 
      ```
      // in windows
      docker run -v ${PWD}:/var/logs`...
      
      // in linux
      docker run -v $(pwd):/var/logs`...
      ```

## Overview of deploying a Containerized App
  - Start from an image
  - Docker file control file system operations needed to "add" the apps to the image
    + Mostly create folder, copy files, etc.
  - Push the result image into a repository (docker hub) for later retrieval & run
  
## Communicate between containers
  - Bridge network: allows containers to talk with each other on the same machine
    + `docker network create --driver bridge <network-name>`: create the network
    + `docker run -d --net=<network-name> --name=<contaienr-name> <image-name>`: create then run the container in detached mode, joining to the specified network
    + Within the container, the application/configuration can use the `container-name` as the host name
  - Other network commands
    + `docker network ls`: list networks
    + `docker network inspect <network-id>`: dumping the network & its nodes
    + `docker network rm <network-name>`: remove the named network
  - Shell into a Container: `docker exec -it <container-id> <shell-name>`
    + `-it`: interactive
    + `<shell-name>`: could be **sh**, **bash** or **PowerShell**, depending on the container itself

## Docker Compose
  - A tool for define & manage multi-container docker application
    + Define services using a YAML configuration file (e.g. "docker-compose.yml")
    + Build multiple image, containers; start/stop services; view the service status; stream the logs of running services, etc.
  - Sample "docker-compose.yml"
    ```
    version: '3.x'
    
    services:
      node:
        container_name: nodeapp
        image: nodeapp
        build:
          context: .
          dockerfile: node.dockerfile
        ports:
          - "3000:3000"
        networks:
          - nodeapp-network
        depends_on:
          - mongodb
          
      mongodb:
        container_name: mongodb
        image: mongo
        networks:
          - nodeapp-network
    
    networks:
      nodeapp-network:
        driver: bridge
    ```
  - Basic command
    + `docker-compose build`: build image(s)
    + `docker-compose up`: start the services
    + `docker-compose down`: stop services and delete containers
  - 