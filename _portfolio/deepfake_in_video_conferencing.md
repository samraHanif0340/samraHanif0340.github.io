---
title: "Deepfake in video conferencing"
excerpt: "Python, Deepfake, VirtualCam, OpenCV, Docker"
header:
  teaser: /assets/images/deepfake_live.jpg
date: "2023-01-03" 
---


<img src="{{ BASE_URL }}/assets/images/deepfake_live.jpg" alt="" class="full">


## Introduction
This article explains how one can project a deepfake-ed live video stream of themselves to a meeting on Skype, Zoom, GoogleMeet or any other video calling platform using python, opencv and deepfacelive app running in a docker container. Source code/sample scripts used in this article can be found in this [repository](https://github.com/marib-suItan/deepfacelive-virtualcam).


## Solution Overview

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/deepfacelive-app-flow.png){: .align-center}


## Instructions
+ Start the DeepFaceLive app and make it output a UDP stream
+ Read the UDP stream and write it to a virtual cam

### Start DeepFaceLive app
1. Clone the [deepfacelive repo](https://github.com/iperov/DeepFaceLive)
    ```bash
    git clone https://github.com/iperov/DeepFaceLive
    ```

1. Add the '--network host' flag to docker run in 'start.sh' script to let the container use host network
    ```bash
    cd DeepFaceLive/build/linux/
    nano start.sh

    docker run --network host --ipc host --gpus all -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix -v $DATA_FOLDER:/data/ $CAM0 $CAM1 $CAM2 $CAM3  --rm -it deepfacelive
    ```

1. Build and start the image for Linux, make sure to pass the '-c' flag to pass host machine cameras to docker
    ```bash
    ./start.sh -c
    ``` 

1. Once the app has started, select the camera by choosing a device number from the dropdown. Usually, your system camera is at 0th index, if it is not try a different number. Once successful, your camera stream should be displayed in the window below. 

    <img src="{{ BASE_URL }}/assets/images/deepfacelive-ins-1.png" alt="" class="full">
1. Load a pre-trained face model of a character that you want to swap your face with. Note downloading the model may take some time. 

    <img src="{{ BASE_URL }}/assets/images/deepfacelive-ins-2.png" alt="" class="full">
1. Once you can see a swapped result, selected 'Merged output' from the drpodown and tick the checkbox to output result as a UDP stream. 

    <img src="{{ BASE_URL }}/assets/images/deepfacelive-ins-3.png" alt="" class="full">


    {: .notice--warning}
    Note the udp address [127.0.0.1:1234](), we will use this to read the mpeg transport stream in opencv.

7. To verify that output stream is being written to the defined port, open a terminal window and run the following command. 
    ```bash
    tcpdump -i lo -n udp port 1234
    ```
    The output should look something like below
    <img src="{{ BASE_URL }}/assets/images/deepfacelive-ins-4.png" alt="" class="full">


### Start pyvirtualcam
1. Clone [deepfacelive-virtualcam](https://github.com/marib-suItan/deepfacelive-virtualcam) script: 
    ```bash
    git clone https://github.com/marib-suItan/deepfacelive-virtualcam
    cd deepfacelive-virtualcam/
    ```
1. Create a virtual camera using v4l2loopback and verify its existence.
    ```bash
    sudo apt install v4l2loopback-dkms
    modprobe v4l2loopback video_nr=2
    ls -1 /sys/devices/virtual/video4linux
    ```  
1. Run the python script
    ```bash
    python virtualcam.py
    ```  

1. If everything has been successful you should see a dummy cam option alongside your system camera in other applications.
    <img src="{{ BASE_URL }}/assets/images/deepfacelive-ins-5.png" alt="" class="full">
    <img src="{{ BASE_URL }}/assets/images/deepfacelive-ins-6.png" alt="" class="full">

    {: .notice--warning}
    Note [Chrome]() seems to block virtual cameras for some reason, but Firefox, Skype and MSTeams work just fine.
