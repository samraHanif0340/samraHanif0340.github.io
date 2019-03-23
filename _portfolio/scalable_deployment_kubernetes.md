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

In this project, we will develop a real-time video analytic pipeline (i.e., machine learning on a video stream). Video feed will be streamed from IP cameras into a Kafka queue, processed by a machine learning model, predictions are imprinted onto the frames, processed frames are queued again into another Kafka topic, and then streamed to web for display. Microservices architecture is adopted, where all code is containerized using Dockers and orchestrated using Kubernetes.

The following tools will be used in this project:
+ Kubernetes
+ Docker
+ Golang
+ Kafka & Zookeeper
+ REST
+ TensorFlow Serving API

In this project, the video analytics will focus on a frame-by-frame object classification task, similar to the ImageNet competition. In fact, we will deploy a TensorFlow Serving saved model which is originally trained on ImageNet database.

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
Scalable-Deployment-Kubernetes                     # Main folder
├── goconsumer                                     # Process video frames using machine learning models
│   ├── assets                                     #
│   │   └── imagenetLabels.json                    # Labels for imagenet classes
│   ├── vendor                                     # Dependency files
│   │   ├── confluentkafkago                       #
│   │   │   ├── consumer.go                        # Confluent-Kafka-Go consumer wrapper
│   │   │   └── producer.go                        # Confluent-Kafka-Go producer wrapper
│   │   └── models                                 #
│   │       ├── handler.go                         # Handler for all TensorFlow models
│   │       └── imagenet.go                        # Handler for imagenet TensorFlow model
│   ├── deployment.yml                             # Kubernetes deployment
│   ├── docker-compose.yml                         # Docker deployment
│   ├── dockerfile                                 # To create Docker container
│   ├── main.go                                    #
│   └── message.go                                 # Processes Kafka messages by calling the appropriate TensorFlow models.handler function
├── goproducer                                     # Streams video from IP camera into Kafka
│   ├── vendor                                     # Dependency files
│   │   └── confluentkafkago                       #
│   │       └── producer.go                        # Confluent-Kafka-Go producer wrapper
│   ├── deployment.yml                             # Kubernetes deployment
│   ├── docker-compose.yml                         # Docker deployment
│   ├── dockerfile                                 # To create Docker container
│   └── main.go                                    #
├── govideo                                        # Publishes video to web
│   ├── vendor                                     # Dependency files
│   │   ├── confluentkafkago                       #
│   │   │   └── consumer.go                        # Confluent-Kafka-Go consumer wrapper
│   │   ├── gocv.io                                # Golang client for OpenCV
│   │   └── mjpeg                                  #
│   │       └── stream.go                          # Streams JPEG pictures to web to form motion JPEG (MJPEG)
│   ├── deployment.yml                             # Kubernetes deployment
│   ├── docker-compose.yml                         # Docker deployment
│   ├── dockerfile                                 # To create Docker container
│   └── main.go                                    #
├── tfserving                                      # TensorFlow Serving API
│   ├── resnet                                     # Resnet TensorFlow model
│   │   └── 1538687457                             # TensorFlow model version number
│   │       ├── assets                             #
│   │       │   └── imagenetLabels.json            # Labels for imagenet classes
│   │       ├── variables                          # Variables of TensorFlow saved model
│   │       │   ├── variables.data-00000-of-00001  #
│   │       │   └── variables.index                #
│   │       └── saved_model.pb                     # TensorFlow saved model
│   ├── deployment.yml                             # Kubernetes deployment
│   ├── docker-compose.yml                         # Docker deployment
│   └── dockerfile                                 # To create Docker container
└── zookeeper                                      # Zookeeper and Kafka
    ├── deployment.yml                             # Kubernetes deployment
    └── docker-compose.yml                         # Docker deployment
```

## System Design

### Zookeeper and Kafka

### GoProducer

### GoConsumer

### TFServing

The `deployment.yml` runs two `tfserving` instances in a load balancing manner to serve REST requests from clients at port 8501 and gRPC requests at port 8500.

### GoVideo

To view the video output:
+ go to `<Kubernetes Cluster IP>:30163`. If you are running Minikube at `192.168.99.100`, then go to `192.168.99.100:30163`.
+ go to `<Docker machine IP>:30163`. If you are running Docker natively, go to `127.0.0.1:30163`. If you are running Docker in a virtual machine at `192.168.99.100`, then go to `192.168.99.100:30163`.

The video output will appear as follows: