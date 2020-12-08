---
title: "GRPC C++ with CMake and Docker"
excerpt: "GRPC, C++, CMake, Docker"
header:
  teaser: /assets/images/grpc_01.jpg
date: "2020-12-08" 
---

## Introduction
This article provides a code template to greatly simplify and automate the use of GRPC in a C++ project. The template is built using CMake. A Docker image consisting of all GRPC files and dependencies is also provided for a C++ project.

The following tools will be used in this project:
+ GRPC
+ C++
+ CMake
+ Docker

## Learning Outcome
At the end of this project, we should be able to:
+ Easily write and run GRPC code in C++ without worrying about the GRPC setup for C++.
+ Build GRPC in C++ code with CMake and Docker.

## Code
Find the source code in the [repository](https://github.com/Adaickalavan/grpc-cpp-cmake).

## Key components of code template
1. Key files in this template are `grpc.cmake`, `CMakeLists.txt`, and `Dockerfile`. 
1. The `grpc.cmake` file automatically 
    + installs GRPC via CMake's `FetchContent` module at configure time,
    + generates the C++ sources from the `.proto` files, and
    + performs all necessary file linkage.

## Instructions
### Local: Run code template <a name="local"></a>
1. Download the repository
    ```bash
    $ git clone https://github.com/Adaickalavan/grpc-cpp-cmake.git 
    ```
1. Build source codes using CMake in Linux
    ```bash
    $ cd /path/to/repository/root/grpc-cpp-cmake/
    $ mkdir build
    $ cd build
    $ cmake ..
    $ cmake --build .
    ``` 
1. Run the C++ server
    ```bash
    $ cd /path/to/repository/root/grpc-cpp-cmake/
    $ cd build
    $ ./app/greeter_server
    ```
1. Run the C++ client in a different terminal
    ```bash
    $ cd /path/to/repository/root/grpc-cpp-cmake/
    $ cd build
    $ ./app/greeter_client
    ```

### Docker: Run code template
1. Build image: 
    ```bash
    $ cd /path/to/repository/root/grpc-cpp-cmake
    $ docker build -t grpccpp --network=host .
    ```
1. Run the container: 
    ```bash
    $ cd /path/to/repository/root/grpc-cpp-cmake
    $ docker-compose up
    ```    

### Local: Build and install GRPC

{: .notice--warning}
For most of us, we should build and run all our GRPC C++ code locally by following the instructions given [above](#local). Ideally this step should not be performed, as it has been simplified by the [above](#local) steps. The steps below are only provided for those who are interested in manually installing GRPC system-wide in their computer. See here for further [reference](https://github.com/grpc/grpc/blob/master/BUILDING.md).

1. Set `~/.bash_aliases` or `~/.bashrc` file:
    ```bash
    export MY_INSTALL_DIR=$HOME/.local
    export PATH=$PATH:$MY_INSTALL_DIR/bin
    ```

1. Install pre-requisites and CMake
    ```bash
    $ sudo apt-get install -y build-essential autoconf libtool pkg-config
    $ sudo apt-get install -y cmake
    ```

1. Clone gRPC repository
    ```bash
    $ git clone -b v1.28.1 https://github.com/grpc/grpc
    $ cd grpc
    $ git submodule update --init
    ```

1. Builds and install gRPC
    ```bash
    $ cd grpc
    $ mkdir -p cmake/build
    $ cd cmake/build
    $ cmake -DgRPC_INSTALL=ON \
        -DgRPC_BUILD_TESTS=OFF \
        -DCMAKE_INSTALL_PREFIX=$MY_INSTALL_DIR \
        ../..
    $ make
    $ make install
    ```

<!-- 
## Project Structure

The project structure is as follows:

```text
opencl                                 # Repository root
├── apps                               # Source code
|   ├── Ch1
|   |   ├── matvec.c                   # C source file
|   |   └── matvec.cl                  # Cl source file
|   ├── Ch2
|   |   ├── context_count.c            # C source file
|   |   ├── device_ext_test.c
|   |   ...
|   .
|   .
|   .
|   ├── Ch8
|   |   ├── buffer_test.cl             # Cl source file
|   |   ├── buffer_test.cpp            # C++ source file
|   |   ...
|   └── CMakeLists.txt                 # CMake build file
├── assets                             # Reference materials 
|   ├── libpng-1.4.0-manual.pdf      
|   ├── OpenCL device model.jpg      
|   ├── OpenCL operators.jpg
|   ├── OpenCL scalar data types.jpg
|   └── OpenCL vector data types.jpg
├── bin                                # Executables
|   ├── ...                              
|   ...
├── libs                               # Local libraries
│   └── util                     
|       ├── CMakeLists.txt             # Library-level CMake build file
|       ├── util.c                     
|       ├── util.cpp                   
|       ├── util.h                     
|       └── util.hpp                   
├── .dockerignore
├── .gitignore
├── CMakeLists.txt                     # Top-level CMake build file
└── README.md                                 
```
-->