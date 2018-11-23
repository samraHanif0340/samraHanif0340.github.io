---
title: "Dockerized Microservices, RTSP Video, and Kafka"
excerpt: "Data pipeline: Golang, Python, internal/external networking, zookeeper"
header:
  teaser: assets/images/gopher_docker.jpg
---

{% include toc %}

## Info

{: .notice--warning}
This page is under construction. Please visit later for more updates.

A real time streaming protocol([RTSP](https://en.wikipedia.org/wiki/Real_Time_Streaming_Protocol)) video is streamed from a website using OpenCV, via a dockerized Kafka queue in Golang, and processed by a standalone Python code running in the host machine (i.e., outside of Docker). This project covers multiple important concepts. builds a REST web API to POST and GET word defintions to/from a dictionary stored in a MongoDB database. Dockerfile and Docker-compose files are provided to containerize the deployment of Go code and MongoDB database.

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
DataPipeline                # Main folder
├── goproducerrtsp          # To import initial data into MongoDB
│   ├── vendor              # To build image for initializing MongoDB
│   ├── .env                # To build image for initializing MongoDB
│   ├── Docker-compose.yml  # To build image for initializing MongoDB
│   ├── vendor              # To build image for initializing MongoDB
│   └── icecream.json       # Seed data
├── kafkapc                 # Helper files for Kafka producer and consumer
│   ├── kafka-consumer.go   #
│   └── kafka-producer.go   #
├── pythonconsumerrtsp      # To compose 3 containerized services
├── pythonconsumerrtsp2              # Dockerfile to build `icecream` api image
└── zookeeper               # Zookeeper and Kafka 
    └── Docker-compose.yml  # 
```

## Design

{: .notice--success}
Further extension of functionality/code and more description of the code will be proivded if requested.

To run the current code as found in the repository, issue the following Docker commands:

![pipeline](/assets/images/pipeline_01.jpg){:height="100%" width="100%" .align-center}

### Zookeeper and Kafka

```yml
version: '3'
services:

  #Zookeeper
  zookeeper:
    image: confluentinc/cp-zookeeper
    container_name: zookeeper
    restart: always
    networks:
      - dockerNet
    ports:
      - 2181:2181 #Zookeeper
    environment:
      - ZOOKEEPER_SERVER_ID=1
      - ZOOKEEPER_CLIENT_PORT=2181
      - ZOOKEEPER_TICK_TIME=2000
      - ZOOKEEPER_SERVERS=zookeeper:22888:23888

  #Kafka
  kafka:
    image: confluentinc/cp-kafka
    container_name: kafka
    restart: unless-stopped
    depends_on:
      - zookeeper
    networks:
      - dockerNet
    ports:
      - 9092:9092 #Kafka broker - access port for external 
      # - 29092:29092 #Kafka broker - access port for internal
      # - 1099:1099 #JMX
    environment:
      - KAFKA_BROKER_ID=1
      - KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181
      # - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT_HOST://localhost:9092, PLAINTEXT://kafka:29092    
      - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT_HOST://192.168.99.100:9092, PLAINTEXT://kafka:29092
      - KAFKA_LISTENER_SECURITY_PROTOCOL_MAP=PLAINTEXT:PLAINTEXT, PLAINTEXT_HOST:PLAINTEXT
      - KAFKA_INTER_BROKER_LISTENER_NAME=PLAINTEXT
      - KAFKA_JMX_PORT=9991
      - KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1

networks:
  dockerNet:
    driver: bridge
```

+ Create and run all containers
  + `docker-compose up`
+ Tear down all containers and stored volume
  + `docker-compose down -v`


### GoProducerRTSP

+ Build image of Go code
  + `docker build -t goproducerrtsp .`


### PyConsumerRTSP

