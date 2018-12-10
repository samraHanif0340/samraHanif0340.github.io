---
title: "Timeseries Database and Visualization"
excerpt: "InfluxDB, Grafana, and Golang"
header:
  teaser: assets/images/data_engineering_01.jpg
---

{% include toc %}

{: .notice--success}
This page is under construction. Please check back later for updates.

## Info

A real-time time-series database is implemented using InfluxDB and is visualized using Grafana.

![pipeline](/assets/images/timeseries_01.jpg){:height="100%" width="100%" .align-center}

<!-- ## Learning Outcome

The [repository](https://github.com/Adaickalavan/DataPipeline) contains the following:

+ Docker-compose file for Zookeeper and Kafka services
+ Go code
  + Dockerized Zookeeper and Kafka images
  + Dockerized Kafka producer with GoCV (openCV) library
+ Python code
  + Kafka consumer for RTSP video
  + OpenCV and dummy signal processing code

At the end of this project, we should be able to:
+ manage Go dependencies using `dep`
+ stream RTSP video
+ use dockerized Zookeeper and Kafka
+ dockerize Golang code
+ perform internal/external Docker networking
+ use dockerized GoCV (i.e., Golang client for OpenCV)
+ use of Sarama library (Golang client for Kafka) and Kafka-Python library (Python client for Kafka)
+ perform docker multistage build to reduce image size
+ use Kafka for inter-language communication (between Golang and Python)
+ building scalable and fault tolerant data pipeline with multiple producers and consumers

## Project Structure

The project structure is as follows:

```text
DataPipeline                # Main folder
├── goproducerrtsp          # To grab frames from RTSP video website and to enqueue in Kafka
│   ├── vendor              # Dependency vendor files generated using 'dep ensure'
│   │   └── ...             #
│   ├── .env                # Environment variables
│   ├── Docker-compose.yml  # To instantiate Docker container
│   ├── Dockerfile          # To build Docker image
│   ├── Gopkg.lock          # Dependency version file generated using 'dep ensure'
│   ├── Gopkg.toml          # Dependency version file generated using 'dep ensure'
│   └── main.go             # Go code producing to Kafka
├── pythonconsumerrtsp      # To consume from Kafka in Python, outside Docker environment
│   ├── dataprocessing      # Template folder for signal processing
│   │   ├── __init__.py     # Package file
│   │   └── alg.py          # Dummy signal processing object: Computes average pixel value
│   ├── kafkapc_python      # Kafka producer and consumer using kafka-python library
│   │   └── __init__.py     #
│   ├── message             # Kafka message handling function
│   │   └── __init__.py     #
│   ├── .env                # Environment variables
│   ├── Docker-compose.yml  # To instantiate Docker container
│   ├── Dockerfile          # To build Docker image
│   ├── main.py             # Python code consuming from Kafka
│   └── requirements.txt    # Imported libraries in the python code
├── pythonconsumerrtsp2     # Duplicate of `pythonconsumerrtsp` to illustrate code scalability
│   ├── ...                 #
│       .                   #
│       .                   #
│       .                   #
│   └── ...                 #
└── zookeeper               # Zookeeper and Kafka
    └── Docker-compose.yml  # To instantiate Docker container for Zookeeper and Kafka
```

## System Design



The complete system design and data flow of this project is illustrated by the following image.

![pipeline](/assets/images/pipeline_01.jpg){:height="100%" width="100%" .align-center} -->