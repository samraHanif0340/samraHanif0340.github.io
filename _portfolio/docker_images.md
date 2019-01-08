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
    + Golang:alpine
    + Librdkafka
    + Confluent-Kafka-Go

2. **GoCV-Debian**

   Automated docker build image for:
   + Golang:latest (Debian)
   + OpenCV 4.0.0

3. **GoCV-Alpine**

   Automated docker build image for:
   1. gocv-alpine:4.0.0-build-stage
      + Golang:alpine
      + OpenCV 4.0.0
      + FFmpeg
  
   Automated docker build image for:
   1. gocv-alpine:4.0.0-runtime
      + Golang:alpine
      + OpenCV 4.0.0
      + FFmpeg
      + Only necessary files included

4. **GoCVKafka**

   Automated docker build image for:
   + FROM adaickalavan/gocv-alpine:4.0.0-build-stage
     + Alpine
     + Golang
     + OpenCV 4.0.0
     + FFmpeg

   + added in this-docker-file
     + GoCV
     + Librdkafka
     + Confluent-Kafka-Go
