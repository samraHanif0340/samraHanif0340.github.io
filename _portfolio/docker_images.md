---
title: "Docker Images"
excerpt: "Golang, GoCV, Librdkafka, Confluent-Kafka-Go"
header:
  teaser: assets/images/gopher_docker.jpg
---

{% include toc %}

## Info

Below, I have listed several helpful Docker images which I maintain at [DockerHub](https://hub.docker.com/u/adaickalavan).

## Repository

The [repository](https://github.com/Adaickalavan/docker-images) contains the following:

1. **GoKafka**

    Automated docker build image for:

    + Alpine
    + Golang
    + Librdkafka
    + Confluent-Kafka-Go

    This docker image can be used as a build-stage image to build applications which depend on Golang and confluent-kafka-go.

2. **GoCV-Debian**

   Automated docker build image for:

   + Golang:latest (Debian based image)
   + OpenCV 4.0.0

   This docker image can be used as a build-stage image to build applications which depend on Golang and OpenCV 4.0.0.

3. **GoCV-Alpine**
   1. gocv-alpine:4.0.0-build-stage

   2. gocv-alpine:4.0.0-runtime

4. **GoCVKafka**

   Automated docker build image for:

   + FROM adaickalavan/gocv-alpine:4.0.0-build-stage
     + Alpine
     + Golang
     + OpenCV 4.0.0

   + FROM this-docker-file
     + GoCV
     + Librdkafka
     + Confluent-Kafka-Go

    This docker image can be used as a build-stage image to build applications which depend on Golang, GoCV, Confluent-Kafka-Go.
