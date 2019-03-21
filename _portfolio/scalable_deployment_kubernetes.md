---
title: "Scalable Deployment in Kubernetes"
excerpt: "Video Analytics, TensorFlow Serving, Golang, Kafka, Docker, REST"
header:
  teaser: /assets/images/scalable_deployment_kubernetes_01.jpg
feature_row:
  - image_path: /assets/images/scalable_deployment_kubernetes_03.jpg
  - image_path: /assets/images/scalable_deployment_kubernetes_04.jpg
---

{% include feature_row %}

{% include toc %}

{: .notice--success}
This page is under construction. Please check back later for updates.

## Introduction

In this project, we will develop a real-time video analytic (i.e., machine learning on video input) pipeline. Video feed will be streamed from IP cameras into a Kafka queue, processed by a machine learning model, the predictions will be imprinted onto the video, queued again into another Kafka topic, and then streamed to web for display. Microservices architecture is adopted, where all code is containerized using Dockers and orchestrated using Kubernetes.

The following tools will be used in this project:
+ Kubernetes
+ Docker
+ Golang
+ Kafka
+ REST
+ TensorFlow Serving API

In this project, the video analytic will be on object recognition similar to ImageNet competition. In fact, we will deploy a TensorFlow Serving saved model, originally trained on ImageNet database, in our project.

The complete system design and data flow of this project is illustrated by the following image.

![pipeline](/assets/images/scalable_deployment_kubernetes_02.jpg){:height="100%" width="100%" .align-center}

## Learning Outcome

Find the source code in the [repository](https://github.com/Adaickalavan/Scalable-Deployment-Kubernetes).

At the end of this project, we should be able to:
+ ...
+ ...
+ ...

## Project Structure

The project structure is as follows:

```text
Scalable-Deployment-Kubernetes  # Main folder
├── goconsumer                  # Docker container for processing video frames using the machine learning models
│   ├── deployment.yml  
│   ├── dockerfile
│   └── main.go
├── goproducer                  # Docker container for streaming data from IPCam into Kafka
│   ├── deployment.yml  
│   ├── dockerfile
│   └── main.go
├── govideo                     # Docker container for publishing video to web
│   ├── deployment.yml  
│   ├── dockerfile
│   └── main.go
├── tfserving                   # Docker container for TensorFlow Serving API
│   ├── deployment.yml  
│   ├── dockerfile
│   └── main.go
└── zookeeper                    # Docker container for Zookeeper and Kafka
    ├── deployment.yml  
    ├── dockerfile
    └── main.go
```