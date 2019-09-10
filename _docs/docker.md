---
title: "Docker"
---

## Docker File
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
    LABEL Author Adaickalavan Meiyappan

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

## Configuration
+ Need to set proxy for Docker if we use Docker behind a corporate proxy, e.g., `http://10.0.0.0:8080/`. Hence, create a `http-proxy.conf` file at `/etc/systemd/system/docker.service.d/` such that `/etc/systemd/system/docker.service.d/http-proxy.conf` file contains the following.
    ```
    [Service]
    Environment="HTTP_PROXY=http://10.0.0.0:8080/"
    Environment="HTTPS_PROXY=https://10.0.0.0:8080/"
    Environment="NO_PROXY=http://172.16.0.0/"
    ```  
    Note that we can set no proxy for internal IP addresses such as `http://172.16.0.0/`.

+ Restart the Docker daemon to start using the new proxy setting.
    ```bash
    $ systemctl daemon-reload
    $ service docker restart
    ```