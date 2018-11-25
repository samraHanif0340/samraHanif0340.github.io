---
title: "Dockerized Microservices, RTSP Video, and Kafka"
excerpt: "Data pipeline: Golang, Python, Docker internal/external networking"
header:
  teaser: assets/images/gopher_docker.jpg
---

{% include toc %}

{: .notice--warning}
This page is under construction. Please visit later for more updates.

## Info

A real time streaming protocol ([RTSP](https://en.wikipedia.org/wiki/Real_Time_Streaming_Protocol)) video is streamed from a website using [OpenCV](https://opencv.org/) into a Kafka topic and consumed by a signal processing application. This project serves to highlight and demonstrate various important data engineering concepts. The data pipeline in detail is as follows:

+ `GoProducerRTSP` module: An RTSP video is streamed from a third party website using dockerized [GoCV](https://gocv.io/) (a golang wrapper of openCV) code written in Golang. The video is enqueued into a dockerized Kafka topic. RTSP is prevalent in security cameras and commercial camera systems.
+ Zookeeper and Kafka modules: Zookeeper and Kafka container instances are spun from dockerized images from [Confluent](https://docs.confluent.io/current/installation/docker/docs/image-reference.html).
+ `PyConsumerRTSP` module: The video is consumed from the Kafka topic by a standalone Python code running in the host machine (i.e., outside of Docker). The fetched video frames are displayed using openCV. Some simple computation is performed on each video frame and printed to screen by the Python code. This signal procesing portion serves as a template (or placeholder) for real practical signal processing code.
+ `PyConsumerRTSP2` module: This is a duplicate of `PyConsumerRTSP` package. Running two consumers from two different consumer groups simultaneously, namely `PyConsumerRTSP` and `PyConsumerRTSP2`, which consume from the same Kafka topic demonstrates scalability of the data pipeline.

## Repository

The [repository](https://github.com/Adaickalavan/DataPipeline) contains the following:

+ Docker-compose file for Zookeeper and Kafka services
+ Go code
  + Dockerized Zookeeper and Kafka images
  + Dockerized Kafka producer with GoCV (openCV) library
+ Python code
  + Kafka consumer for RTSP video
  + Dummy signal processing code

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
│   └── main.go             # Go producing to Kafka
├── pythonconsumerrtsp      # To consume from Kafka in Python, outside docker environment
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
│   ├── main.py             # Python consuming from Kafka
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

{: .notice--success}
Further description of the system design will be proivded if requested by readers.

The complete system design and data flow of this project is illustrated by the following image.

![pipeline](/assets/images/pipeline_01.jpg){:height="100%" width="100%" .align-center}

Each component of the system will be explored in detail in the following sections.

### Zookeeper and Kafka

First, the Zookeeper and Kafka services are started as containers using docker images from Confluent. Below is the `Docker-compose.yml` file.

```yml
version: '3'
services:

  #Zookeeper
  zookeeper:
    image: confluentinc/cp-zookeeper
    container_name: zookeeper
    restart: always #Ensures that intermittent failures in the Docker environment do not result in unnecessary failures of the service.
    networks:
      - dockerNet
    ports:
      - 2181:2181 #Zookeeper port - access port for external communication
    environment:
      - ZOOKEEPER_SERVER_ID=1
      - ZOOKEEPER_CLIENT_PORT=2181
      - ZOOKEEPER_TICK_TIME=2000

  #Kafka
  kafka:
    image: confluentinc/cp-kafka
    container_name: kafka
    restart: always #Ensures that intermittent failures in the Docker environment do not result in unnecessary failures of the service.
    depends_on:
      - zookeeper
    networks:
      - dockerNet
    ports:
      - 9092:9092 #Kafka broker - access port for external communication
    environment:
      - KAFKA_BROKER_ID=1
      - KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181
      # - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT_HOST://localhost:9092, PLAINTEXT://kafka:29092 #For native Docker (e.g., in Windows 10)
      - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT_HOST://192.168.99.100:9092, PLAINTEXT://kafka:29092 #For Docker Tool (e.g., in Windows 7)
      - KAFKA_LISTENER_SECURITY_PROTOCOL_MAP=PLAINTEXT:PLAINTEXT, PLAINTEXT_HOST:PLAINTEXT
      - KAFKA_INTER_BROKER_LISTENER_NAME=PLAINTEXT
      - KAFKA_JMX_PORT=1099
      - KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR=1

networks:
  dockerNet:
    driver: bridge
```

Containers are conected via an internal Docker network named `dockerNet` to facilitate internal communicaion among containers. Containers can accessed one another by their `container_name`.

Zookeeper port number is `2181` and is also published to localhost making it accessible to programs from outside the Docker environment.

The `KAFKA_ADVERTISED_LISTENERS` variable is set to `192.168.99.100:9092` and `kafka:29092`. This makes Kafka accessible from inside the Docker through `kafka:29092` and also from outside the container through `192.168.99.100:9092` by advertising its location on the Docker host.

Remember to use your Docker machine IP (e.g., `192.168.99.100` as shown in above code) if you are using Docker Tool (or a VM). Otherwise, if you are using native Docker, please replace the IP addresses with `localhost` as shown commented in the above code.

Issue the following Docker commands in the working directory of the Docker-compose.yml file:

+ To run Zookeeper and Kafka containers
  + `docker-compose up`
+ To tear down all containers and stored volume at the end of operation
  + `docker-compose down -v`
  + `docker system prune`
  + `docker volume prune`
  + `docker network prune`

### GoProducerRTSP

Secondly, the Kafka producer code, in Golang, to stream RTSP video into Kafka topic `timeseries_1` is shown below. [Sarama](https://github.com/Shopify/sarama) library is used as the Golang client for Kafka producer. A further wrapper for Golang producer (and consumer) built on top of Sarama and [wvanbergen](github.com/wvanbergen/kafka/consumergroup) libraries is provided for ease of use in my [kafkapc](github.com/adaickalavan/kafkapc) package.

```go
package main

import (
	"encoding/json"
	"fmt"
	"image"
	"io"
	"log"
	"os"
	"time"

	"github.com/Shopify/sarama"
	"github.com/adaickalavan/kafkapc"
	"gocv.io/x/gocv"
)

//Hooks that may be overridden for testing
var inputReader io.Reader = os.Stdin
var outputWriter io.Writer = os.Stdout

// Instantiate a producer
var producer sarama.AsyncProducer

func main() {
	//Sarama logger
	sarama.Logger = log.New(outputWriter, "[saramaLog]", log.Ltime)

	//Create a Kafka producer
	var brokers = []string{os.Getenv("KAFKAPORT")}
	var err error
	producer, err = kafkapc.CreateKafkaProducer(brokers)
	if err != nil {
		panic("Failed to connect to Kafka. Error: " + err.Error())
	}
	//Close producer to flush(i.e., push) all batched messages into Kafka queue
	defer func() { producer.Close() }()

	// Capture video
	webcam, err := gocv.OpenVideoCapture(os.Getenv("RTSPLINK"))
	if err != nil {
		panic("Error in opening webcam: " + err.Error())
	}

	// Stream images from RTSP to Kafka message queue
	frame := gocv.NewMat()
	for {
		if !webcam.Read(&frame) {
			continue
		}

		// Type assert frame into RGBA image
		imgInterface, err := frame.ToImage()
		if err != nil {
			panic(err.Error())
		}
		img, ok := imgInterface.(*image.RGBA)
		if !ok {
			panic("Type assertion of pic (type image.Image interface) to type image.RGBA failed")
		}

		//Form the struct to be sent to Kafka message queue
		doc := Result{
			Pix:      img.Pix,
			Channels: frame.Channels(),
			Rows:     frame.Rows(),
			Cols:     frame.Cols(),
		}

		//Prepare message to be sent to Kafka
		docBytes, err := json.Marshal(doc)
		if err != nil {
			log.Fatal("Json marshalling error. Error:", err.Error())
		}
		msg := &sarama.ProducerMessage{
			Topic:     os.Getenv("TOPICNAME"),
			Value:     sarama.ByteEncoder(docBytes),
			Timestamp: time.Now(),
		}
		//Send message into Kafka queue
		producer.Input() <- msg

		//Print time of receiving each image to show the code is running
		fmt.Fprintf(outputWriter, "---->>>> %v\n", time.Now())
	}
}

//Result represents the Kafka queue message format
type Result struct {
	Pix      []byte `json:"pix"`
	Channels int    `json:"channels"`
	Rows     int    `json:"rows"`
	Cols     int    `json:"cols"`
}
```


GoCV library is used to stream the RTSP video

[Dep](https://golang.github.io/dep/) is used for Golang dependency management. To initialize, issue `dep init` command in the module's main working directory. Commands `dep ensure` ensures the project is in sync and `dep ensure -update` updates all the dependencies.

```yml
FROM denismakogon/gocv-alpine:3.4.2-buildstage as build-stage

#Copy the local package files (from development computer) to the container's workspace (docker image)
COPY . /go/src/app 
# BUild the app command inside the container
RUN go install app

FROM denismakogon/gocv-alpine:3.4.2-runtime

#Author label
LABEL Author Adaickalavan Meiyappan

#Copy executable binary file
COPY --from=build-stage /go/bin/app /go/bin/app

#Run executable when container starts
ENTRYPOINT ["/go/bin/app"]
```

+ Instantiate a container from the image
  + `docker-compose up`

```yml
version: '3'

services:
  #Producer to obtain data from RTSP and write into Kafka queue
  rtsp:
    image: goproducerrtsp
    container_name: goproducerrtsp
    environment:
      - TOPICNAME=timeseries_1
      - KAFKAPORT=kafka:29092
      - RTSPLINK=rtsp://184.72.239.149/vod/mp4:BigBuckBunny_175k.mov
    networks:
      - zookeeper_dockerNet
        # To ensure that the containers in different docker-compose files communicate with each other,
        # we place them on the same network. The full network name from 'kafka-zookeeper-mongo' docker-compose
        # file is 'kafka_zookeeper_mongo_dockerNet'.
    privileged: true  

networks:
  zookeeper_dockerNet:
    external: true #Label the other docker-compose network as an external network to the current docker-compose file
```



Issue the following commands in the working directory of the Docker-compose.yml file:

+ To run Zookeeper and Kafka containers
  + `docker-compose up`

+ Build image of Go code
  + `docker build -t goproducerrtsp .`





### PyConsumerRTSP

### PyConsumerRTSP2

