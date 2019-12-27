---
title: "Python C Extension"
excerpt: "Python, C, OpenMP, Docker"
header:
  teaser: /assets/images/python_c_extension_01.jpg
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

### Python code
1. The main Python code lies in `/example/pycextension.py` file.
1. A dummy Numpy matrix is generated and passed into a Python wrapper function, namely, `lib.feature.synthesize_matrix()`.
1. The Python wrapper function calls an underlying C shared object to perform some random computation and returns a modified matrix.
    + Note that the data type of the output matrix is `float` whereas the data type of the input matrix was `int`.
1. In the `/lib/feature.py` file, 
    + The shared library is opened using `CDLL` function.
    + The argument and return types of the C function are specified.
    + Some translations between Python and C types are as follows:
      | Python                                              	| C/C++ type 	| Comments                                               	|
      |-----------------------------------------------------	|------------	|--------------------------------------------------------	|
      | ctypes.c_int                                        	| int        	|                                                        	|
      | ctypes.c_double                                     	| double     	|                                                        	|
      | numpy.ctypeslib.ndpointer(dtype=np.uint8, ndim=1)   	| uchar[]    	| Pass a 1 dimensional Numpy array of type numpy.unint8  	|
      | numpy.ctypeslib.ndpointer(dtype=np.float32, ndim=2) 	| float[][]  	| Pass a 2 dimensional Numpy array of type numpy.float32 	| 
    + Refer to the [ctype documentation](https://docs.python.org/3/library/ctypes.html) and [numpy documentation](https://docs.scipy.org/doc/numpy-1.15.0/reference/routines.ctypeslib.html#numpy.ctypeslib.ndpointer) for a complete translation of types between Python and C.

### C library
1. The C libraries reside in `/lib/cpp/lib/` folder.
1. The code in `synthhesize_matrix()` function was parallelized using OpenMP. OpenMp `#pragma` command was utilised to set the number of threads, collapse nested `for` loops, and to implement a worksharing `for` loop.
1. The C code is compiled with the help of CMake as defined in CMakeLists.txt file.
1. In the `/lib/cpp/CMakeLists.txt` file, we set the output directory of the shared object `.so` to be `/lib/cpp/out` as follows. 
    ```cmake
    set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/out/)
    ```
    This simplifies the process of finding and referring to the shared object in our Python code.

### Python depedencies
1. Python dependencies are managed in `/requirements.txt` file.
1. Please refer to the programming [guide](https://adaickalavan.github.io/docs/python/#dependencies) for explanation on how to manage, generate, and install, python dependencies

### Docker 
1. All necessary Python, pip, CMake, and OpenMP libraries are installed by the Dockerfile.
1. The Dockerfile also automatically rebuilds the C library shared object each time the Docker image is built via `docker-compose up` command.
