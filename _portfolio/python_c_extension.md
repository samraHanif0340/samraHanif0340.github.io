---
title: "Python C Extension"
excerpt: "Python, C, OpenMP, Docker"
header:
  teaser: /assets/images/python_c_extension_01.jpg
# gallery_01:
#   - url: /assets/images/webrtc_02.jpg
#     image_path: /assets/images/webrtc_02.jpg
#   - url: /assets/images/webrtc_03.jpg
#     image_path: /assets/images/webrtc_03.jpg   
date: "2019-12-16"    
---

## Introduction

In this project, we show how to develop a C extension for Python. The C library will be interfaced with Python using `ctypes`. Additionally, the C library will be accelerated via parallelization using OpenMP. The entire code is finally containerized using Docker for easy deployment.

The following tools will be used in this project:
+ Python
+ C
+ OpenMP
+ Docker

## Code

Find the source code in the [repository](https://github.com/Adaickalavan/python-c-extension).

## Learning Outcome

At the end of this project, we should be able to:
+ Call C/C++ compiled code from Python using the `ctypes` and `numpy.ctypeslib` modules
+ Parallelize C code with OpenMP 
+ Dockerize Python, C, and OpenMP code

## Project Structure

The project structure is as follows:

```text
python-c-extension                   # Repository root
├── example                      
│   └── pycextension.py              # Main Python code
├── lib                       
│   ├── cpp                          # C folder
│   │   ├── bin                      # 
│   │   ├── build                    # C build system
│   │   ├── lib                     
│   │   │   └── feature               
│   │   |       ├── CMakeLists.txt 
│   │   |       ├── feature.c        # C library
│   │   |       └── feature.h#       # C header file
│   │   ├── out 
│   │   |   └── libfeature.so        # C library Shared object
│   │   └── CMakeLists                
│   └── feature.py                   # Python library
├── .dockerignore
├── .env                             # Config file 
├── .gitignore
├── docker-compose.yml               # Docker deployment
├── Dockerfile                       # To create Docker container
├── README.md                        
└── requirements.txt                 # Python dependencies                
```

## Instructions
1. Download the code using `git clone https://github.com/Adaickalavan/python-c-extension.git`
1. Install Docker and Docker Compose in your computer
1. Run the code using Docker
    ```bash
    $ cd /path/to/repository/root
    $ docker build -t pycextension .
    $ docker-compose up
    ```
1. If you intend to run the project locally without Docker
    + Change the `ROOT` variable in the `.env` file to refer to the repository's root.
    + Build the C code using:
        ```bash
        $ cd /path/to/pythoncextension/lib/cpp/
        $ cmake -E make_directory build 
        $ cmake -E chdir ./build cmake -DCMAKE_BUILD_TYPE=Release ..
        $ cmake --build ./build
        ```
    + Run the Python code:
        ```bash
        $ python /path/to/pythoncextension/example/pycextension.py   
        ```    

## System Design
Certain key aspects of the system is further explored in the following sections.

### pycextension.py
1. A Go server named `sdpServer` acts as the signalling server for WebRTC, as well as hosting both the `Publish` and`Join` webpages.  
    ```go
    mux.HandleFunc("/sdp", handlerSDP(s))
    mux.HandleFunc("/join", handlerJoin)
    mux.HandleFunc("/publish", handlerPublish)
    mux.Handle("/static/", http.StripPrefix("/static/", http.FileServer(http.Dir("./static/"))))
    ```

### Python depedencies - requirements.txt
1. Please refer to the programming [guide](https://adaickalavan.github.io/docs/python/#dependencies) for explanation on how to manage, generate, and install, python dependencies. 

### Docker - Dockerfile 
1. All necessary Python, pip, CMake, and OpenMP libraries are installed. 
1. The Dockerfile also automatically rebuilds the C library shared object each time the Docker image is built via `docker-compose up` command.
