---
title: "Dockerized Microservices, RTSP Video, and Kafka"
excerpt: "Data pipeline: Golang, Python, Docker internal/external networking"
header:
  teaser: assets/images/gopher_docker.jpg
---

{% include toc %}

## Info

{: .notice--warning}
This page is under construction. Please visit later for more updates.

A real time streaming protocol([RTSP](https://en.wikipedia.org/wiki/Real_Time_Streaming_Protocol)) video is streamed from a website using [OpenCV](https://opencv.org/), via a dockerized Kafka queue in Golang, and processed by a standalone Python code running in the host machine (i.e., outside of Docker). This project covers multiple important data engineering concepts, such as:

+ Streaming of RTSP video from a third party website using GoCV (a golang implementation of openCV). The code is containerized with Dockerfile and Docker-compose file. RTSP is prevalent in security cameras and commercial camera systems.
+ 
  

## Repository

The [repository](https://github.com/Adaickalavan/DataPipeline) contains the following:

+ Go code
  + Dockerized Zookeeper and Kafka images
  + Dockerized Kafka producer with GoCV (openCV) library
+ Python code
  + Kafka consumer for RTSP video
  + Dummy signal processing code

## Project Structure

The project structure is as follows:

```text
DataPipeline                # Main folder
├── goproducerrtsp          # To grab frames from RTSP video website and to enqueue in Kafka
│   ├── vendor              # Dependency vendor files generated using 'dep ensure'
│   │   └── ...             #
│   ├── .env                # Environment variables
│   ├── Docker-compose.yml  # To instantiate Docker container
│   ├── Dockerfile          # To build Docker image
│   ├── Gopkg.lock          # Dependency version file generated using 'dep ensure'
│   ├── Gopkg.toml          # Dependency version file generated using 'dep ensure'
│   └── main.go             # Go producing to Kafka
├── kafkapc                 # Helper files for Kafka producer and consumer in Golang
│   ├── kafka-consumer.go   # Kafka consumer using Sarama and wvanbergen library
│   └── kafka-producer.go   # Kafka producer using Sarama library
├── pythonconsumerrtsp      # To consume from Kafka in Python, outside docker environment
│   ├── dataprocessing      # Template folder for signal processing
│   │   ├── __init__.py     # Package file
│   │   └── alg.py          # Dummy signal processing object: Computes average pixel value
│   ├── kafkapc_python      # Kafka producer and consumer using kafka-python library
│   │   └── __init__.py     #
│   ├── message             # Kafka message handling function
│   │   └── __init__.py     #
│   ├── .env                # Environment variables
│   ├── Docker-compose.yml  # To instantiate Docker container
│   ├── Dockerfile          # To build Docker image
│   ├── main.py             # Python consuming from Kafka
│   └── requirements.txt    # Imported libraries in the python code
├── pythonconsumerrtsp2     # Duplicate of `pythonconsumerrtsp` to illustrate code scalability
│   ├── ...                 #
│       .                   #
│       .                   #
│       .                   #
│   └── ...                 #
└── zookeeper               # Zookeeper and Kafka
    └── Docker-compose.yml  # To instantiate Docker container for Zookeeper and Kafka
```

## System Design

{: .notice--success}
Further extension of functionality/code and more description of the code will be proivded if requested.

The complete system design of this project is illustrated by the following image.

![pipeline](/assets/images/pipeline_01.jpg){:height="100%" width="100%" .align-center}

Each component of the syste will be explored in detail in the following sections.

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
      - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT_HOST://localhost:9092, PLAINTEXT://kafka:29092
      # - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT_HOST://192.168.99.100:9092, PLAINTEXT://kafka:29092
      - KAFKA_LISTENER_SECURITY_PROTOCOL_MAP=PLAINTEXT:PLAINTEXT, PLAINTEXT_HOST:PLAINTEXT
      - KAFKA_INTER_BROKER_LISTENER_NAME=PLAINTEXT
      - KAFKA_JMX_PORT=9991
      - KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1

networks:
  dockerNet:
    driver: bridge
```

To run the current code as found in the repository, issue the following Docker commands:

+ Create and run all containers
  + `docker-compose up`
+ Tear down all containers and stored volume
  + `docker-compose down -v`
  + `docker system prune`
  + `docker volume prune`
  + `docker network prune`


### GoProducerRTSP

+ Build image of Go code
  + `docker build -t goproducerrtsp .`


### PyConsumerRTSP

