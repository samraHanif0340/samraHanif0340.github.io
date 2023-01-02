---
title: "Docker"
---

## Installation
1. Install Docker Engine and Docker Compose from the official [site](https://docs.docker.com/install/linux/docker-ce/ubuntu/). 

## Dockerfile
+ Containerization is packaging an application, its dependencies, and its configuration into a single deployable unit, called an image.
+ Failed Docker containers can be restarted automatically.
+ Can be run on Linux, Mac, Windows
+ Aids reusability, refactorability, scalability, and maintainability of code.
+ Each command used to generate Docker image  generates a new intermediate image via a delta — essentially capturing only what changed from the previous intermediate step.
+ The following is a representative Docker file with multistage build feature. Build the image using the command `$ docker build -t <container name>` .
    ```dockerfile
    FROM golang AS baseimage

    # Enable GO111MODULE
    ENV GO111MODULE=on

    # Author label
    LABEL Author Marib Sultan

    # Install build tools
    RUN apt-get update && apt-get -y install g++ cmake git pkg-config

    # Set the working directory
    WORKDIR /src

    # Fetch dependencies first; they are less susceptible to change on every build and will therefore be cached for speeding up the next build
    COPY ./go.mod ./go.sum ./
    RUN go mod download

    # Copy the local package files (local computer) to the container (docker image)
    COPY . .

    # Build the executable to `/app`. Mark the build as statically linked.
    RUN CGO_ENABLED=0 go build -o ./app .

    # Multistage build
    FROM scratch

    # Import the compiled executable from the first stage.
    COPY --from=baseimage src/app /app
    COPY --from=baseimage src/.env /.env

    # Run the compiled binary when the conainer starts
    ENTRYPOINT ["/app“]
    ```

## Docker Compose
+ To instantiate the container image, run a docker compose file by executing `$ docker-compose up`. Docker compose file contains instructions to instantiate the image, set environment variables, setup networks, etc. 
    ```yml
    version: '3'
    services:
    goconsumer:
        image: goconsumer
        container_name: goconsumer 
        restart: on-failure
        ports:
        - 30000:30000
        environment:
        - TOPICNAMEIN=videocam
        - KAFKAPORTIN=kafka1:19094
        - GROUPNAME=goconsumer
        - DISPLAY=$DISPLAY
        - MODELURLS={"imagenet_0":"http://tfsimagenet:8501/v1/models/tfModel:predict"} 
        - LABELURLS={"imagenet_0":"/go/src/app/assets/imagenetLabels.json"} 
        volumes:
        - /tmp/.X11-unix:/tmp/.X11-unix 
        devices:
        - /dev/video0:/dev/video0 
        networks:
        - zookeeper_dockerNet 
            # To ensure containers in different docker-compose 
            # files communicate  with each other, we place them 
            # on the same network. The complete network name is # 'zookeeper_dockerNet'. It is derived by joining 
            # the name of the folder from which the network 
            # originates (i.e., zookeeper) and the name of the 
            # network (i.e., dockerNet).

    networks:
    zookeeper_dockerNet:
        external: true 
        # Label the other docker-compose network as an external 
        # network to the current docker-compose file
    ```

## Proxy
1. Need to set proxy for Docker if we use Docker behind a corporate proxy.

1. Create a `http-proxy.conf` file.
    ```bash
    $ touch http-proxy.conf
    ```
1. Populate the `http-proxy.conf` file with proxy info.    
    ```
    [Service]
    Environment="HTTP_PROXY=http://10.0.0.0:8080/"
    Environment="HTTPS_PROXY=https://10.0.0.0:8080/"
    Environment="NO_PROXY=http://172.16.0.0/"
    ```  
    Note that we can set no proxy for internal IP addresses such as `http://172.16.0.0/`.

1. Create `/etc/systemd/system/docker.service.d/` directory and move the file `http-proxy.conf` into it.
    ```bash
    $ mkdir /etc/systemd/system/docker.service.d
    $ mv -f http-proxy.conf /etc/systemd/system/docker.service.d/
    ```

1. Restart the Docker daemon to start using the new proxy setting.
    ```bash
    # Reload systemctl so new settings are read
    $ sudo systemctl daemon-reload
    # Verify docker environment is properly set
    $ sudo systemctl show docker --property Environment
    # Restart docker service 
    $ sudo systemctl restart docker
    ```

1. To use `RUN apt-get` command inside `dockerfile` behind a proxy, set proxy for Linux `apt` using the environment `ENV` variable in the `dockerfile` as follows.
    ```dockerfile
    # Dockerfile
    FROM ubuntu
    ...
    ENV http_proxy "http://10.0.0.0:8080/"
    ENV https_proxy "https://10.0.0.0:8080/"
    ...
    ```            

## Build
+ Required tools
    ```bash
    $ sudo apt-get install sshpass
    ```
+ An example shell script named `remote.sh` is provided to build, save, transfer, load, and run interactively, a docker image in a remote server.
    ```sh
    #!/bin/bash

    # Example command to execute this script:
    # source remote.sh <user name> <password> <server address> <destination directory> <container name>

    # Arguments
    USER=$1       #Username, e.g., admin
    PASS=$2       #Password, e.g., abcd1234
    ADD=$3        #Server address, e.g., 10.193.200.15
    DST=$4        #Destination directory, e.g., /src/$USER
    CONTAINER=$4  #Container name, e.g., mycontainer

    # Build Docker image locally
    docker build \
        -t ${CONTAINER} \
        --network=host \
        .
    # Save image locally
    docker save -o ./${CONTAINER}.tar ${CONTAINER}
    # Push image to remote server
    sshpass -p ${PASS} scp ${PWD}/${CONTAINER}.tar ${USER}@${ADD}:${DST}
    # Load docker in remote server
    sshpass -p ${PASS} ssh ${USER}@${ADD} "docker load -i ${DST}/${CONTAINER}.tar"
    # Run interactive docker container in detached mode in remote server 
    sshpass -p ${PASS} ssh ${USER}@${ADD} "docker run --rm \
        -it \
        -d \
        --network=host \
        --privileged \
        --env="XAUTHORITY=/tmp/.docker.xauth" \
        --env="QT_X11_NO_MITSHM=1" \
        --volume=/tmp/.X11-unix:/tmp/.X11-unix:rw \
        --device=/dev/dri \
        --memory=100g \
        --user=$(id -u):$(id -g) \
        --name=${CONTAINER} \
        ${CONTAINER}
    "
    ```

## Interactive usage
1. Start a bash interactively inside a docker container
    ```bash
    $ docker exec -ti <container name> bash
    ```
1. Exit without stopping the container. Interactive mode turns into daemon mode.
    ```bash
    ctrl-p ctrl-q
    ```

## Display
1. Enable display access from docker to linux desktop
    ```bash
    $ xhost +
    ```