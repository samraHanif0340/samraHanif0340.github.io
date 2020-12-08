---
title: "GRPC C++ with CMake and Docker"
excerpt: "GRPC, C++, CMake, Docker"
# header:
#   teaser: /assets/images/opencl_01.jpg
date: "2020-12-08" 
---

{: .notice--success}
This page is under construction. Please check back later for updates.

## Introduction
This project aspires to greatly simplify and automate the use of GRPC in C++. The project is built using CMake. A Docker image consisting of GRPC dependencies is also provided optionally built using CMake and Docker. Currently, 

The following tools will be used in this project:
+ GRPC
+ C++
+ CMake
+ Docker

## Learning Outcome
At the end of this project, we should be able to:
+ Easily write GRPC code in C++ without worrying about the GRPC setup for C++
+ Build GRPC in C++ code with CMake and Docker.

## Code
Find the source code in the [repository](https://github.com/Adaickalavan/grpc-cpp-cmake).

## Instructions to build and run
1. If we use this project as a template, namely, the `grpc.cmake`, `CMakeLists.txt`, and `Dockerfile` files, we   a typical the code
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
1. Run the C++ client
    ```bash
    $ cd /path/to/repository/root/grpc-cpp-cmake/
    $ cd build
    $ ./app/greeter_client
    ```

## Instructions to build and run the code inside Docker
1. Build image: 
    ```bash
    $ cd /path/to/repository/root/grpc-cpp-cmake
    $ docker build -t cppclient --network=host .
    ```
1. Run the container: 
    ```bash
    $ cd /path/to/repository/root/grpc-cpp-cmake
    $ docker-compose up
    ```    

### Build and install GRPC locally

{: .notice--warning}
We should be able to build and run all our C++ code locally by following the instructions given above. Hence, this steps are not needed. However, if you desire to build and install GRPC locally, the instructions are given below. For further reference see here for [reference](https://github.com/grpc/grpc/blob/master/BUILDING.md#pre-requisites).

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

<!-- in high performance computing for heterogenous platforms (i.e., CPU/GPU). We present updated C/C++ code adhering to OpenCL 2.0 standard for the exercises in the "OpenCL in Action" book by Matthew Scarpino. Read the [book](https://www.manning.com/books/opencl-in-action). It is a good comprehensive book, suitable for beginners, which teaches OpenCL beginning from the basics.

 -->

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

## Steps to get started with OpenCL
1. Install OpenCL SDK 
  + For Intel processors: [website](https://software.intel.com/en-us/opencl-sdk/choose-download)

1. Install and run `clinfo` to show complete information of OpenCL platforms and devices.
```bash
$ sudo apt install clinfo
$ clinfo
```

## Exercise list
For easy reference, the solution code is organised by chapter and named according to the exercise. 

Build the C/C++ code using:
```bash
$ cd /path/to/repository/root/opencl
$ cmake -E make_directory build
$ cmake -E chdir ./build cmake -DCMAKE_BUILD_TYPE=Release ..
$ cmake --build ./build
```

Run the desired code as follows. Assuming the desired code is `callback_8_6`, then run:
```bash
$ cd /path/to/repository/root/opencl
$ ./bin/callback_8_6
```

The complete code listing is given in the table below. -->
