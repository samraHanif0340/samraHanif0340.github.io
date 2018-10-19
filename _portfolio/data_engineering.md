---
title: "Dockerized Microservices in Golang, Python, Kafka"
excerpt: "Example of data pipeline engineering with REST, MongoDB, and input sensors"
header:
  teaser: assets/images/gopher_docker.jpg
---

{% include toc %}

## Info

This project builds a REST web API to POST and GET word defintions to/from a dictionary stored in a MongoDB database. Dockerfile and Docker-compose files are provided to containerize the deployment of Go code and MongoDB database.

## Repository

The [repository](https://github.com/Adaickalavan/DictionaryAPI) contains the following:

+ Dockerfile
+ Dockercompose
+ Go code
+ REST API
+ MongoDB

## Design

To run the current code as found in the repository, issue the following Docker commands:

+ Build image of Go code
  + `docker build -t "dictionaryapi" .`
+ Create and run all containers
  + `docker-compose up`
+ Tear down all containers and stored volume
  + `docker-compose down -v`

{: .notice--success}
Further extension of functionality and more description of the code will be proivded later. This repository is a work in progress.
