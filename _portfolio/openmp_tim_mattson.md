---
title: "Parallel programming with OpenMP - Tim Mattson"
excerpt: "OpenMP, C/C++, Docker"
header:
  teaser: /assets/images/openmp_01.jpg
---

{: .notice--success}
This page is under construction. Please check back later for updates.

## Introduction

This project explores the use of OpenMP for parallel programming in C/C++. We provide the code solutions to C/C++ and OpenMP coding exercises from the Intro to OpenMP by Tim Mattson, Intel Corp, online course at Youtube. Watch the course [video](https://www.youtube.com/watch?v=nE-xN4Bf8XI&list=PLLX-Q6B8xqZ8n8bwjGdzBJ25X2utwnoEG) and read the course [notes](https://www.openmp.org/wp-content/uploads/Intro_To_OpenMP_Mattson.pdf). 

All the code is containerized using Dockers.

A very useful OpenMP 3.1 API C/C++ syntax quick reference card is available from OpenMP [here](https://github.com/Adaickalavan/OpenMP-Tim-Mattson/blob/master/assets/OpenMP3.1-CCard.pdf)

The following tools will be used in this project:
+ C/C++
+ OpenMP
+ Docker

## Learning Outcome

Find the source code in the [repository](https://github.com/Adaickalavan/OpenMP-Tim-Mattson).

<!-- 
At the end of this project, we should be able to:
+ Build a WebRTC video and data broadcasting service in Go.
+ Build a signalling server in Go and automate the exchange of SDPs (Session Description Protocol) between the webpages and the server. 
+ Write and deploy Javascript in the browser to initiate WebRTC connections and to communicate with the signalling server.
+ Host the `Publish` and `Join` webpages on a Go server.
+ Remember the publisher and client so as to reconnect upon a loss of connection.
+ Perform dependency management using Go Module.  -->

<!-- ## Project Structure

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
``` -->

<!-- ## Instructions
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
1. The video and data connection will dynamically resume if the publisher or client disconnects and rejoins later. -->

<!-- ## System Design
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
    ```     -->

## List of exercises

<!-- In order to run the solutions for the exercise, the following steps -->

For easy reference, each code solution is named according to the slide on which they appear in the course [notes](https://www.openmp.org/wp-content/uploads/Intro_To_OpenMP_Mattson.pdf).


<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;border-color:#aaa;}
.tg td{font-family:Arial, sans-serif;font-size:14px;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:#aaa;color:#333;background-color:#fff;}
.tg th{font-family:Arial, sans-serif;font-size:14px;font-weight:normal;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:#aaa;color:#fff;background-color:#f38630;}
.tg .tg-5rba{background-color:#FCFBE3;border-color:#656565;text-align:left;vertical-align:top}
.tg .tg-di1h{border-color:#656565;text-align:center;vertical-align:middle}
.tg .tg-oxga{background-color:#FCFBE3;border-color:#656565;text-align:left;vertical-align:middle}
.tg .tg-9ikn{border-color:#656565;text-align:left;vertical-align:middle}
.tg .tg-2bev{border-color:#656565;text-align:left;vertical-align:top}
</style>
<table class="tg" style="undefined;table-layout: fixed; width: 777px">
<colgroup>
<col style="width: 341px">
<col style="width: 436px">
</colgroup>
  <tr>
    <th class="tg-di1h">Exercise</th>
    <th class="tg-di1h">Instructions to run</th>
  </tr>
  <tr>
    <td class="tg-oxga">Slide 38 - Parallel 'Hello World' program</td>
    <td class="tg-oxga" rowspan="6">1) <span style="font-weight:bold">Main file</span>: `/apps/tutorial.cpp`<br>2) <span style="font-weight:bold">Build</span>: `docker build -t openmp .` on `Dockerfile`<br>3) <span style="font-weight:bold">Set</span>: `command: /src/bin/tutorial` in `docker-compose.yaml` file <br>4) <span style="font-weight:bold">Run</span>: `docker-compose up` on `docker-compose.yaml` file</td>
  </tr>
  <tr>
    <td class="tg-9ikn">Slide 48 - Compute Pi in serial</td>
  </tr>
  <tr>
    <td class="tg-5rba">Slide 52 - Compute Pi in parallel with false sharing</td>
  </tr>
  <tr>
    <td class="tg-2bev">Slide 57 - Compute Pi in parallel with padding</td>
  </tr>
  <tr>
    <td class="tg-5rba">Slide 69 - Compute Pi in parallel with synchronization</td>
  </tr>
  <tr>
    <td class="tg-2bev">Slide 88 - Compute Pi with WorkSharing</td>
  </tr>
  <tr>
    <td class="tg-5rba">Slide 119 - Mandel Brot</td>
    <td class="tg-5rba">1) <span style="font-weight:bold">Main file</span>: `/apps/mandel.c`<br>2) <span style="font-weight:bold">Build</span>: `docker build -t openmp .` on `Dockerfile`<br>3) <span style="font-weight:bold">Set</span>: `command: /src/bin/mandel` in `docker-compose.yaml` file<br>4) <span style="font-weight:bold">Run</span>: `docker-compose up` on `docker-compose.yaml` file<br></td>
  </tr>
  <tr>
    <td class="tg-2bev">Slide 124 - Linked list computed serially</td>
    <td class="tg-9ikn" rowspan="3">1) <span style="font-weight:bold">Main file</span>: `/apps/linked.c`<br>2) <span style="font-weight:bold">Build</span>: `docker build -t openmp .` on `Dockerfile`<br>3) <span style="font-weight:bold">Set</span>: `command: /src/bin/linked` in `docker-compose.yaml` file<br>4) <span style="font-weight:bold">Run</span>: `docker-compose up` on `docker-compose.yaml` file<br></td>
  </tr>
  <tr>
    <td class="tg-5rba">Slide 128 - Linked list in parallel without Tasks</td>
  </tr>
  <tr>
    <td class="tg-2bev">Slide 143 - Linked list in parallel with Tasks</td>
  </tr>
  <tr>
    <td class="tg-5rba">Slide 166 - Producer Consumer</td>
    <td class="tg-5rba">1) <span style="font-weight:bold">File</span>: `/apps/prodCons.c`<br>2) <span style="font-weight:bold">Build</span>: `docker build -t openmp .` on `Dockerfile`<br>3) <span style="font-weight:bold">Set</span>: `command: /src/bin/prodCons` in `docker-compose.yaml` file<br>4) <span style="font-weight:bold">Run</span>: `docker-compose up` on `docker-compose.yaml` file<br></td>
  </tr>
  <tr>
    <td class="tg-2bev">Slide 177 - Parallel Monte Carlo computation of PI</td>
    <td class="tg-9ikn" rowspan="3">1) <span style="font-weight:bold">File</span>: `/apps/pi_mc.c`<br>2) <span style="font-weight:bold">Build</span>: `docker build -t openmp .` on `Dockerfile`<br>3) <span style="font-weight:bold">Set</span>: `command: /src/bin/pi_mc` in `docker-compose.yaml` file<br>4) <span style="font-weight:bold">Run</span>: `docker-compose up` on `docker-compose.yaml` file<br></td>
  </tr>
  <tr>
    <td class="tg-5rba">Slide 181 - Thread safe random number generator</td>
  </tr>
  <tr>
    <td class="tg-2bev">Slide 185 - Leap frog method to avoid overlapped random number sequence</td>
  </tr>
</table>