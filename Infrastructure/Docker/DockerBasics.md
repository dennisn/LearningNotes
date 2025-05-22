# Docker Basics

  - Basic concept:
    + Container image <--> class
    + Container       <--> instance
  - Container vs. Virtualisation
    + Virtualisation: at hardware level --> create "virtual" hardware
    + Container: at the OS (kernel) level --> create virtual OS
    + Advantage: smaller footprint, faster start up, while ensure consistent deployment of production (by ensure all dependencies are included)
  - Software
    + Docker Desktop: good for local installation
    + Play with Docker: 4h-limit free playground for Docker Hub account, good to try & maybe learn
  - Tools to manage containers: Docker Swarm, Kubernetes
  - Microservices: split each application feature as its own small app, or microservices
    + Advantage: can scale up/down, patch/upgrade each microservices **separatedly** based on demand
  - Cloud-native microservices: microservices that makes use of cloud capabilities: self-healing, self-scale, auto-update, etc.
  - Docker services: allows multiple identical copy of containers running together as a service
    + automatic start new containers when one or more are killed
    + serve at the same port, also do load balancing

## Overview of deploying a Containerized App
  - Start from an image
  - Docker file control file system operations needed to "add" the apps to the image
    + Mostly create folder, copy files, etc.
  - Push the result image into a repository (docker hub) for later retrieval & run
  
## Misc
  - For high-availability, nodes are often in odd number
    + when communication is disrupted, at least one group will have majority of nodes, and can continues with changes, while the minority group will switch to read-only mode until re-connection