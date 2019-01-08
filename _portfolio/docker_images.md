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

2. **GoCV-Debian**

   Automated docker build image for:
   + Golang:latest (Debian)
   + OpenCV 4.0.0

3. **GoCV-Alpine**

   1. gocv-alpine:4.0.0-build-stage
      + Alpine
      + Golang
      + OpenCV 4.0.0
      + FFmpeg

   2. gocv-alpine:4.0.0-runtime
      + Alpine
      + Golang
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

   + FROM this-docker-file
     + GoCV
     + Librdkafka
     + Confluent-Kafka-Go
