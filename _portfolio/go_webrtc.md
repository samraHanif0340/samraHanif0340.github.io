---
title: "WebRTC Video & Data Streaming"
excerpt: "WebRTC, Golang, Javascript, HTML, Docker"
header:
  teaser: /assets/images/webrtc_01.jpg
gallery_01:
  - url: /assets/images/webrtc_02.jpg
    image_path: /assets/images/webrtc_02.jpg
  - url: /assets/images/webrtc_03.jpg
    image_path: /assets/images/webrtc_03.jpg      
---

## Introduction

In this project, we will develop a real-time video and data broadcasting service using WebRTC in Golang. Video feed will be streamed from the publisher's web camera through WebRTC media API to multiple browser-based end clients. Simultaneously, some accompanying data will be streamed from the publisher to the multiple end-clients through the WebRTC data channel. All the code is containerized using Dockers.

The following tools will be used in this project:
+ WebRTC
+ Docker
+ Golang
+ Javascript
+ HTML

## Learning Outcome

Find the source code in the [repository](https://github.com/Adaickalavan/Go-WebRTC).

At the end of this project, we should be able to:
+ Build a WebRTC video and data broadcasting service in Go.
+ Build a signalling server in Go and automate the exchange of SDPs (Session Description Protocol) between the webpages and the server. 
+ Write and deploy Javascript in the browser to initiate WebRTC connections and to communicate with the signalling server.
+ Host the `Publish` and `Join` webpages on a Go server.
+ Remember the publisher and client so as to reconnect upon a loss of connection.
+ Perform dependency management using Go Module. 

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
│   ├── join.html            # Webpage template for client to join the broadcast 
│   └── publish.html         # Webpage template for publisher to broadcast
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
1. To run locally:
    + Ensure `GO111MODULE=on` in your terminal.
    + Run `go install` in the project folder.
    + Then run the executable, i.e., `Go-WebRTC`.
1. To run the code in Docker, do the following:
    + Run `docker build -t gowebrtc .` in the project folder.
    + Then run `docker-compose up`.
1. Go to `localhost:8088/publish` web page which will start capturing video using your webcam. This video, along with some accompanying data, will be broadcast to multiple clients.
1. Then open another tab in your browser, and go to `localhost:8088/join` to see the broadcasted video and data. Multiple clients can view the broadcast by joining the same weblink. 
1. The video and data connection will dynamically resume if the publisher or client disconnects and rejoins later.

The end product of this system is illustrated in the following image.

{% include gallery id="gallery_01" caption="Video and data broadcast to multiple clients through WebRTC. (Left) Publisher's captured video at http://domainId:8088/publish. (Right) Multiple client viewing the broadcast at http://domainId:8088/join. Random text and random box is brodcasted via the data track, whereas video is broadcasted via the video track."%}

## System Design
Certain key aspects of the system is further explored in the following sections.

### Go Server - main.go
1. A Go server named `sdpServer` acts as the signalling server for WebRTC, as well as hosting both the `Publish` and`Join` webpages.  
    ```go
    mux.HandleFunc("/sdp", handlerSDP(s))
    mux.HandleFunc("/join", handlerJoin)
    mux.HandleFunc("/publish", handlerPublish)
    mux.Handle("/static/", http.StripPrefix("/static/", http.FileServer(http.Dir("./static/"))))
    ```
1. The `handlerSDP()` function returns a handler which
    + receives a video track from the publisher 
    + adds a video track and data track to each connected client
1. To mimic data streaming, date-time information and random bounding boxes are generated in the server which is then transmitted to the client using the data channel.      

### WebRTC API - main.go
1. The peer connection function `pc.OnTrack()` has been written in a manner  which allows either publisher or client to first connect to the SDP server. 
    + Hence, clients may connect to the server even before a publisher is available.   
    + This feature also enables the video & data reception on client side to resume automatically when a publisher reconnects after a momentary loss of connection. 
1. The peer connection function `pc.OnDataChannel()` generates date-time information and random bounding boxes, which are sent to the client via the data channel. 

### Javascript - connect.js
1. The `connect.js` file 
    + starts WebRTC media and data connection for both publisher and client
    + communicates with the signalling server by triggering `async function sendToServer()` function.
1. A canvas is used to superimpose the data from WebRTC data channel onto the video being displayed to the end-client. The relevant functions are  
    + `function readyToPlayVideo(event)`
    + `function draw(top, left, vidH, vidW)`
    + `function drawRectangle(ctx, x, y, w, h)`
    + `function drawText(ctx, text)`

### Docker - dockerfile 
1. The code uses the latest Go Module for dependency management.
1. Hence, the following lines are included in the `dockerfile` to enable Go Module and to automatically download the dependencies inside the Docker image.
    ```dockerfile
    # Enable GO111MODULE
    ENV GO111MODULE=on

    # Automatically install Go dependencies
    COPY ./go.mod ./go.sum ./
    RUN go mod download
    ```    