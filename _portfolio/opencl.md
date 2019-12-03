---
title: "High performance computing with OpenCL"
excerpt: "OpenCL, C/C++"
header:
  teaser: /assets/images/opencl_01.jpg
date: "2019-12-03" 
---

{: .notice--success}
This page is under construction. Please check back later for updates.

## Introduction

This project explores the use of OpenCL for high performance computing for heterogenous platforms (i.e., CPU/GPU). 

The following tools will be used in this project:
+ C/C++
+ OpenCL

## Learning Outcome

Find the source code in the [repository](https://github.com/Adaickalavan/opencl).

At the end of this project, we should be able to:
+ Use OpenCL in C/C++ for high performance computing. 
+ Build C/C++ code with CMake.

<!-- ## Project Structure

The project structure is as follows:

```text
OpenMP-Tim-Mattson                   # Main folder
├── apps                             # Executables for each exercises
|   ├── CMakeLists.txt               # CMake build for executables
|   ├── linked.c                     
|   ├── mandel.c
|   ├── pi_mc.c
|   ├── prodCons.c           
|   └── tutorial.c                   
├── assets                           # Assets
|   ├── Intro_To_OpenMP_Mattson.pdf  # Tutorial slides
|   └── OpenMP3.1-CCard.pdf          # OpenMP reference card
├── libs                             # Local libraries
│   ├── computepi                     
|   │   ├── CMakeLists.txt           # Library-level CMake build file
|   │   ├── computepi.cpp            # Source file
|   │   └── computepi.h              # Header file
│   ├── helloworld                    
|   │   ├── CMakeLists.txt           # Library-level CMake build file
|   │   ├── helloworld.cpp           # Source file
|   │   └── helloworld.h             # Header file
│   └── random                       
|       ├── CMakeLists.txt           # Library-level CMake build file
|       ├── random.c                 # Source file
|       └── random.h                 # Header file
├── .dockerignore
├── .gitignore
├── CMakeLists.txt                   # Top-level CMake build file
├── docker-compose.yml               # Docker deployment
├── dockerfile                       # To create Docker container
└── README.md                                 
```

## List of exercises

For easy reference, each solution code is named according to the slide on which they appear in the course [notes](https://www.openmp.org/wp-content/uploads/Intro_To_OpenMP_Mattson.pdf). Instructions to build, setup commands, and to run the solutions for each exercise is given in the following table.

<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;border-color:#aaa;}
.tg td{font-family:Arial, sans-serif;font-size:14px;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:#aaa;color:#333;background-color:#fff;}
.tg th{font-family:Arial, sans-serif;font-size:14px;font-weight:normal;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:#aaa;color:#fff;background-color:#f38630;}
.tg .tg-5rba{background-color:#FCFBE3;border-color:#656565;text-align:left;vertical-align:top}
.tg .tg-di1h{border-color:#656565;text-align:center;vertical-align:middle}
.tg .tg-oxga{background-color:#FCFBE3;border-color:#656565;text-align:left;vertical-align:middle}
.tg .tg-9ikn{border-color:#656565;text-align:left;vertical-align:middle}
.tg .tg-2fy7{background-color:#ffffff;border-color:#656565;text-align:left;vertical-align:middle}
.tg .tg-2bev{border-color:#656565;text-align:left;vertical-align:top}
</style>
<table class="tg">
<colgroup>
<col style="width: 30%">
<col style="width: 70%">
</colgroup>
  <tr>
    <th class="tg-di1h">Exercise</th>
    <th class="tg-di1h">Instructions to run</th>
  </tr>
  <tr>
    <td class="tg-oxga">Slide 38 - Parallel 'Hello World' program</td>
    <td class="tg-2fy7" rowspan="6">1) <span style="font-weight:bold">File</span>: `/apps/tutorial.cpp`<br>2) <span style="font-weight:bold">Build</span>: `docker build -t openmp .` on `Dockerfile`<br>3) <span style="font-weight:bold">Set</span>: `command: /src/bin/tutorial` in `docker-compose.yaml` file <br>4) <span style="font-weight:bold">Run</span>: `docker-compose up` on `docker-compose.yaml` file</td>
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
    <td class="tg-5rba">1) <span style="font-weight:bold">File</span>: `/apps/mandel.c`<br>2) <span style="font-weight:bold">Build</span>: `docker build -t openmp .` on `Dockerfile`<br>3) <span style="font-weight:bold">Set</span>: `command: /src/bin/mandel` in `docker-compose.yaml` file<br>4) <span style="font-weight:bold">Run</span>: `docker-compose up` on `docker-compose.yaml` file<br></td>
  </tr>
  <tr>
    <td class="tg-2bev">Slide 124 - Linked list computed serially</td>
    <td class="tg-9ikn" rowspan="3">1) <span style="font-weight:bold">File</span>: `/apps/linked.c`<br>2) <span style="font-weight:bold">Build</span>: `docker build -t openmp .` on `Dockerfile`<br>3) <span style="font-weight:bold">Set</span>: `command: /src/bin/linked` in `docker-compose.yaml` file<br>4) <span style="font-weight:bold">Run</span>: `docker-compose up` on `docker-compose.yaml` file<br></td>
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
</table> -->