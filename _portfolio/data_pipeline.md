---
title: "Dockerized Microservices, RTSP Video, and Kafka"
excerpt: "Data pipeline: Golang, Python, internal/external networking, zookeeper"
header:
  teaser: assets/images/gopher_docker.jpg
---

{% include toc %}

## Info

A real time streaming protocol([RTSP](https://en.wikipedia.org/wiki/Real_Time_Streaming_Protocol)) video is streamed from a website through a dockerized Kafka queue in Golang and processed by standalone Python code running in localhost.  This project builds a REST web API to POST and GET word defintions to/from a dictionary stored in a MongoDB database. Dockerfile and Docker-compose files are provided to containerize the deployment of Go code and MongoDB database.

## Repository

The [repository](https://github.com/Adaickalavan/DataPipeline) contains the following:

+ Go code
+ Python code
+ Dockerized RTSP video streaming
+ Dockerized openCV
+ Zookeeper
+ Kafka
+ Docker internal networking between containers
+ Docker external networking with processes outside Docker
+ Dockerfile
+ Dockercompose

## Project Structure

The project structure is as follows:

```text
project                             # Assumed to be located at C:/goWorkspace/
└── src                             #
    └── icecreamapi                 # Main folder
        ├── seeddata                # To import initial data into MongoDB
        │   ├── Dockerfile          # To build image for initializing MongoDB
        │   └── icecream.json       # Seed data
        ├── vendor                  # Folder containing dependencies
        │   ├── credentials         # Dependant package `credentials`  
        │   │   ├── jwtoken.go      # Create and authenticate JWT
        │   │   └── login.go        # Hash and compare login passwords
        │   ├── database            # Dependant package `database`  
        │   │   ├── connection.go   # Generic database connection function
        │   │   └── product.go      # Database CRUD operations
        │   ├── document            # Dependant package `document`  
        │   │   └── icecream.go     # `icecream` document to be stored in MongoDB
        │   └── handler             # Dependant package `handler`  
        │       └── respond.go      # Generic http response functions
        ├── Docker-compose.yml      # To compose 3 containerized services
        ├── Dockerfile              # Dockerfile to build `icecream` api image
        ├── handlers.go             # Handlers for RESTful operation
        ├── main.go                 # Main file of Go code
        └── verify.go               # Verify user login and obtain claims
```

## Design

{: .notice--success}
Further extension of functionality/code and more description of the code will be proivded if requested.

To run the current code as found in the repository, issue the following Docker commands:

![pipeline](/assets/images/pipeline_01.jpg){:height="100%" width="100%" .align-center}


+ Build image of Go code
  + `docker build -t "dictionaryapi" .`
+ Create and run all containers
  + `docker-compose up`
+ Tear down all containers and stored volume
  + `docker-compose down -v`


