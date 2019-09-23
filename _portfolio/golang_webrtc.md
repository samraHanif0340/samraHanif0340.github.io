---
title: "WebRTC Video Streaming"
excerpt: "pion WebRTC, Golang, Javascript, HTML, Docker"
header:
  teaser: /assets/images/webrtc_01.jpg
---

## Introduction

In this project, we will develop a real-time video broadcasting service using WebRTC in Golang. Video feed will be streamed from one camera through WebRTC API to multiple browser-based end clients. All code is containerized using Dockers.

The following tools will be used in this project:
+ Docker
+ Golang
+ WebRTC
+ Javascript
+ HTML

<!-- The complete system design and data flow of this project is illustrated by the following image. -->

<!-- {% capture fig_pipeline %}
![pipeline](/assets/images/scalable_deployment_kubernetes_02.jpg){:height="100%" width="100%" .align-center}
{% endcapture %} -->

<!-- <figure id="pipeline">
  {{ fig_pipeline | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>Scalable microservices architecture for video analytics pipeline.</figcaption>
</figure> -->

## Learning Outcome

Find the source code in the [repository](https://github.com/Adaickalavan/Go-WebRTC).

At the end of this project, we should be able to:
+ Build a Golang based WebRTC video broadcasting service in browser.
+ Build a signalling server and automate the exchange of SDPs between the webpages and the Go server. 
+ The webpages `Publish` and `Join` are hosted on the same server as the signalling server.
+ Remember the publisher and client so as to reconnect upon a loss of connection.

## Project Structure

The project structure is as follows:

```text
Go-WebRTC                        # Main folder
├── handler                      # Process video frames using machine learning models
│   └── respond.go
├── static                       # Streams video from IP camera into Kafka
│   └── js
│       └── connect.js           #
├── template                     # Publishes video to web
│   ├── join.html
|   └── publish.html
├── .env
├── docker-compose.yml           # Docker deployment
├── dockerfile                   # To create Docker container
├── go.mod
├── go.sum
├── main.go                      # Main file
└── README.md                                 
```

## Instructions
1. Download the code using `git clone https://github.com/Adaickalavan/Go-WebRTC.git`
1. Ensure `GO111MODULE=on` in your terminal.
1. To run locally:
    + Run `go install` in the project folder.
    + Then run the executable, i.e., `Go-WebRTC`.
1. To run the code in Docker, do the following:
    + Run `docker build -t gowebrtc .` in the project folder.
    + Then run `docker-compose up`.
1. Go to `localhost:8088/publish` web page which will start capturing video using your webcam. This video will be broadcast to multiple clients.
1. Then open another tab in your browser, and go to `localhost:8088/join` to see the broadcasted video. 
