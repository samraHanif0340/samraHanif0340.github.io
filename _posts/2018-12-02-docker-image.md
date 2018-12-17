---
title: "Docker Image"
tags: 
  - docker
categories:
  - docker  
---

Below, I have listed several helpful Docker images which I maintain at [DockerHub](https://cloud.docker.com/u/adaickalavan/).

1. **Confluent-Kafka-Go**

    Automated docker build image for:

    + Alpine
    + Golang
    + Librdkafka
    + Confluent-Kafka-Go

    This docker image can be used as a build-stage image to build applications which depend on Golang and confluent-kafka-go.

1. **GoCV**

   Automated docker build image for:

   + Golang:latest (Debian based image)
   + GoCV
   + OpenCV 4.0.0

   This docker image can be used as a build-stage image to build applications which depend on Golang and GoCV.

1. **GoKafkaCV**

   Automated docker build image for:

   + FROM adaickalavan/confluent-kafka-go
     + Alpine
     + Golang
     + Librdkafka
     + Confluent-Kafka-Go

   + FROM this-docker-file
     + Golang:latest (Debian based image)
     + GoCV
     + OpenCV 4.0.0

    This docker image can be used as a build-stage image to build applications which depend on Golang, Kafka, and GoCV.
