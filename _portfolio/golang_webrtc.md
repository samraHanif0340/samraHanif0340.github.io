---
title: "WebRTC Video Streaming in Golang"
excerpt: "pion WebRTC, Golang, Javascript, HTML"
header:
  teaser: /assets/images/webrtc_01.jpg
# gallery:
#   - image_path: /assets/images/webrtc_02.jpg
---

<!-- {% include gallery %} -->

{: .notice--success}
This page is under construction. Please check back later for updates.

## Introduction

In this project, we will develop a real-time video broadcasting service using WebRTC in Golang. Video feed will be streamed from one camera through WebRTC API to multiple end clients in the browser. All code is containerized using Dockers.

The following tools will be used in this project:
+ Kubernetes
+ Docker
+ Golang
+ Kafka & Zookeeper
+ REST
+ TensorFlow Serving API

In this project, the video analytics will focus on a frame-by-frame object classification task, similar to the ImageNet competition. In fact, we will deploy a TensorFlow Serving saved model which is originally trained on ImageNet database.

The complete system design and data flow of this project is illustrated by the following image.

{% capture fig_pipeline %}
![pipeline](/assets/images/scalable_deployment_kubernetes_02.jpg){:height="100%" width="100%" .align-center}
{% endcapture %}

<figure id="pipeline">
  {{ fig_pipeline | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>Scalable microservices architecture for video analytics pipeline.</figcaption>
</figure>

## Learning Outcome

Find the source code in the [repository](https://github.com/Adaickalavan/Go-WebRTC).

At the end of this project, we should be able to:
+ Build a Golang based WebRTC video broadcasting service in browser.
+ In the original SFU-Minimal code example, the `Publish` and `Join` webpages are hosted on jsfiddle.net. We need to manually transfer the SDPs between the webpage and our server.
+ In this extension, we strive to build a signalling server and automate the exchange of SDPs between the webpages and the server. Moreover, the webpages `Publish` and `Join` are hosted on the same server as the signalling server.
+ Additionally, we try to remember the publisher and client so as to reconnect upon a loss of connection.

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
│   │   ├── models                                 #
│   │   │   ├── handler.go                         # Handler for all TensorFlow models
│   │   │   └── imagenet.go                        # Handler for imagenet TensorFlow model
│   │   └── github.com                             #
│   │       └── pkg                                #
│   │           └── profile                        # Golang code profiler (cpu and memory)
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

## Instructions
1. Download the code using `git clone https://github.com/Adaickalavan/Go-WebRTC.git`
1. Ensure `GO111MODULE=on` in your terminal.
1. To run locally:
    + Run `go install` in the project folder.
    + Then run the executable, i.e., `Go-WebRTC`.
1. To run the code in Docker, do the following:
    + Run `docker build -t webrtc .` in the project folder.
    + Then run `docker-compose up`.
1. Go to `localhost:8088/publish` web page which will start capturing video using your webcam. This video will be broadcast to multiple clients.
1. Then open another tab in your browser, and go to `localhost:8088/join` to see the broadcasted video. 

To terminate the microservices
+ in Kubernetes, execute `kubectl delete -f deployment.yml` in the respective folders of each susbsytem.
+ in Docker, simply close the terminal used to run the subsystem.
