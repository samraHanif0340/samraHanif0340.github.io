---
title: "WebRTC Video & Data Streaming"
excerpt: "pion WebRTC, Golang, Javascript, HTML, Docker"
header:
  teaser: /assets/images/webrtc_01.jpg
---

## Introduction

In this project, we will develop a real-time video and data broadcasting service using WebRTC in Golang. Video feed will be streamed from the publisher's web camera through WebRTC media API to multiple browser-based end clients. Simultaneously, some accompanying data will be streamed from the publisher to the multiple end-clients through WebRTC data channel. All code is containerized using Dockers.

The following tools will be used in this project:
+ Docker
+ Golang
+ WebRTC
+ Javascript
+ HTML

<!-- The end product of this system is illustrated by the following image. -->

<!-- {% capture fig_pipeline %}
![pipeline](/assets/images/webrtc_03.jpg){:height="100%" width="100%" .align-center}
{% endcapture %} -->

<!-- <figure id="pipeline">
  {{ fig_pipeline | markdownify | remove: "<p>" | remove: "</p>" }}
  <figcaption>Video and data broadcast through WebRTC.</figcaption>
</figure> -->

## Learning Outcome

Find the source code in the [repository](https://github.com/Adaickalavan/Go-WebRTC).

At the end of this project, we should be able to:
+ Build a WebRTC video and data broadcasting service in Go.
+ Build a signalling server in Go and automate the exchange of SDPs between the webpages and the Go server. 
+ Write Javascript to initiate WebRTC connections in the browser.
+ Host the `Publish` and `Join` webpages on the same server as the signalling server.
+ Remember the publisher and client so as to reconnect upon a loss of connection.

## Project Structure

The project structure is as follows:

```text
Go-WebRTC                    # Main folder
├── handler                      
│   └── respond.go           # Generate HTTP responses
├── static                       
│   └── js
│       └── connect.js       # Javascript to establish WebRTC connection
├── template                
│   ├── join.html            # Webpage template for user to join the broadcast 
|   └── publish.html         # Webpage template for user to broadcast
├── .env
├── docker-compose.yml       # Docker deployment
├── dockerfile               # To create Docker container
├── go.mod
├── go.sum
├── main.go                  # Main file
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
1. Go to `localhost:8088/publish` web page which will start capturing video using your webcam. This video, along with some accompanying data, will be broadcast to multiple clients.
1. Then open another tab in your browser, and go to `localhost:8088/join` to see the broadcasted video and data. Multiple clients can view the broadcast by joining the same weblink. 
1. The video and data connection will dynamically resume if the publisher or client disconnects and rejoins later.

## System Design
Certain key aspects of the system is further explored in the following sections.

### Docker 
1. The code uses the latest Go Module for dependency management.
1. Hence, the following lines are included in the `dockerfile` to enable Go Module and to automatically download the dependencies inside the Docker image.
    ```dockerfile
    # Enable GO111MODULE
    ENV GO111MODULE=on
    # Automatically install Go dependencies
    COPY ./go.mod ./go.sum ./
    RUN go mod download
    ```    