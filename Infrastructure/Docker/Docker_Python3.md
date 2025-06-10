# Developing Python 3 apps with Docker
  - Sample code is at: https://github.com/geekcap-pluralsight/python-docker 
  
## Setup environment
  - Create the project folders
  - Create virtual environment for Python & related `requirements.txt` file
  
## Build docker image
  - Sample:
    ```
    # base image
    FROM Python
    
    # Set the working directory in the container
    WORKDIR /code
    
    # copy the dependencies files
    COPY requirements.txt .
    
    # install dependencies
    RUN pip install -r requirements.txt
    
    # Copy the program source code from local directory to container
    COPY src/ .
    
    # Command to run the program on container start
    CMD ["python", "./app.py"]
    ```

## Running multiple containers with Docker Compose
  - Features:
    + multiple isolated environments on a single host
    + Preserve volume data from prev. containers
    + Only recreate containers that have changed
    + Support variables

### Sample 
```
services:
  productservice:
    build: product-service
  
  web:
    build: nginx
    ports:
      - "80:80"
```



## Making Your application Production-ready
  - Based on 12-factor methodology for Saas (software as a service): 
    + Benefits: easy to run locally, easy to deploy to production, and easy to debug in production
    + More details: https://12factor.net
  - Logging
    + Basic logging: create a log handler, set it format. Then get a logger by name, set its level and add the handler
    + Docker can see the log from specific container
      ```
      // follow the log from a specific container
      docker log -f <container-id>
      
      // follow the log from last 10 minutes (10m), or 4 hours (4h)
      docker log -f --since 10m <container-id>
      ```
  - Application config with ConfigParser
    + use `.ini` file for configuration, which can be read as the sample
      ```
      config = configparser.ConfigParser().read('db.ini')`
      // read section "mysql", variable "host"
      config['mysql]['host']
      ```
  - Docker Volumes: of 3 types:
    + Anonymous: randomly created & maintained by Docker
    + Host: mounts a local directory/file on the container
    + Named: Created with a name & maintained by Docker
    + Example:
      ```
      // anonymous
      services:
        db:
          volumes:
            - /var/lib/mysql

      // host
      services:
        db:
          volumes:
            - ./data:/var/lib/mysql
      
      // named volumes
      volumes:
        db-volume:
      services:
        db:
          image: mysql
          volumes:
            - db-volume:/var/lib/mysql

      ```
  - Docker compose secrets: blob of data contain sensitive security info (e.g. paswrod, private key, etc.)
    + should not be transmitted over a network, or stored unencrypted locally
    + Use docker secrets to centrally manage, and securely transmit this data to specific containers
    + However, this can really be done in docker swarm --> for docker-compose, use locally file, but map it as "secret": accessible as file under `/run/secrets/`
  - Docker compose Network: can be used to separate different services into different network segments

## Debugging Python application running in containers
  - We can mount the source code from our development environment to container source code --> allow code change to take effect immediately
  - VS Code: use debugpy (ref: https://github.com/microsoft/debugpy). Basic setup:
    + install debugpy via pip: `pip install debugpy`
    + import it to code and listen for VS Code to remotely attach to it (may need to disable other debugger within the code)
      ```
      import debugpy
      debugpy.listent(("0.0.0.0", 5678))
      ```
    + docker also need to expose port 5678 to local host from container
      ```
        ports:
          - "5678:5678"
      ```
    + In VS Code, create lanuch configuration for python remote debugger, as sample below
      ```
      "configurations": [
        {
          "name": "Python: Remote Attach",
          "type": "python",
          "request": "attach",
          "connect": {
            "host": "localhost",
            "port": 5678
          },
          "pathMappings": [
            {
              "localRoot": "${workspaceFolder}/product-service/src",
              "remoteRoot": "/code"
            }
          ]
        }
      ]
      ```
  -
 