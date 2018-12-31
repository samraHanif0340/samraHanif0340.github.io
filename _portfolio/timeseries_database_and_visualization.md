---
title: "Timeseries Database and Visualization"
excerpt: "InfluxDB, Grafana, and Golang"
header:
  teaser: assets/images/timeseries_01.jpg
---

{% include toc %}

{: .notice--success}
This page is under construction. Please check back later for updates.

## Introduction

The figure below shows a typical data engineering pipeline for a production system, where each block is a microservice launched from a Docker container.

![data_engineering](/assets/images/data_engineering_01.jpg){:height="100%" width="100%" .align-center}

It consists of
+ REST api
+ Streaming of sensor inputs
+ Kafka for message queueing/passing
+ Signal processing blocks
+ Database (e.g., MongoDB, InfluxDB)
+ Visualization tool (e.g., Grafana)

In this project, we develop the usage of timeseries database, InfluxDB, and visualization part of a bigger data pipeline. The bigger data pipeline may be learnt from my other two proects, namely, [IceCream API](https://adaickalavan.github.io/portfolio/icecreamapi/) and [RTSP Video, Kafka, and Microservices](https://adaickalavan.github.io/portfolio/rtsp_video_streaming/) projects.

A real-time time-series database is implemented using InfluxDB and is visualized using Grafana.

![pipeline](/assets/images/timeseries_01.jpg){:height="100%" width="100%" .align-center}

## Learning Outcome

The [repository](https://github.com/Adaickalavan/Timeseries-Database-and-Visualization) contains the following:

+ Docker-compose file for Zookeeper and Kafka services
+ Go code
  + Dockerized Zookeeper and Kafka images
  + Dockerized Kafka producer with GoCV (openCV) library
+ Python code
  + Kafka consumer for RTSP video
  + OpenCV and dummy signal processing code

At the end of this project, we should be able to:
+ use dockerized InfluxDB and Grafana
+ visualize time series data in Grafana
+ query time series data in Grafana

+ perform internal/external Docker networking
+ use dockerized GoCV (i.e., Golang client for OpenCV)
+ use of Sarama library (Golang client for Kafka) and Kafka-Python library (Python client for Kafka)
+ perform docker multistage build to reduce image size
+ use Kafka for inter-language communication (between Golang and Python)
+ building scalable and fault tolerant data pipeline with multiple producers and consumers

<!-- ## Project Structure

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