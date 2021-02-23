---
title: "Nvidia"
---

## Instruction to install and run Tensorflow with Nvidia GPU inside Docker container

1. My setting
    ```bash
    NVidia Driver: 450.80.02    
    CUDA: 11.0
    Python: 3.7
    Tensorflow: tensorflow-2.4.0
    ```

1. Add the Docker Engine repository’s key and address to apt’s repository index:
    ```bash
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

1. Update package index and install the Docker engine:
    ```bash
    $ sudo apt-get update && sudo apt-get install docker-ce docker-ce-cli containerd.io
    ```

1. Run hello-world Docker image:
    ```bash
    $ sudo docker run hello-world
    ```

1. Uninstall any previous existing NVidia installations:
    ```bash
    # To uninstall run file based installtion
    $ sudo ./NVIDIA-Linux-x86-310.19.run --uninstall
    # To uninstall package manager based installation
    $ sudo apt-get remove nvidia-430
    ```
    More [uninstallation](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#handle-uninstallation) instructions available at NVidia.   

1. Install recommended drivers for your GPU: 
    ```bash
    $ sudo ubuntu-drivers autoinstall
    ```

1. Launch NVIDIA system management interface:
    ```bash
    $ nvidia-smi
    ```

1. Add NVIDIA Container Toolkit key and address to apt:
    ```bash
    $ distribution=$(. /etc/os-release;echo $ID$VERSION_ID) 
    $ curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add - 
    $ curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
    ```

1. Install NVIDIA Container Toolkit:
    ```bash
    $ sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
    ```

1. Restart Docker:
    ```bash
    $ sudo systemctl restart docker
    ```

1. Check for compatible Tensorflow, NVIDIA driver, and Cuda versions
    + Tensorflow support: https://www.tensorflow.org/install/source#gpu
    + NVidia suport: https://docs.nvidia.com/deploy/cuda-compatibility/index.html#binary-compatibility__table-toolkit-driver

1. Run the NVIDIA system management interface inside of a CUDA Docker container:
    ```bash
    $ docker run -u $(id -u):$(id -g) --gpus all --rm nvidia/cuda:11.0-base nvidia-smi
    ```

1. Run bash inside a Tensorflow-GPU-Docker container. Container uses host machine's GPU. Optionally, it maps a volume from local host to docker container. 
    ```bash 
    $ sudo docker run -u $(id -u):$(id -g) --gpus all -it --network=host -v /home/kyber/workspaces/rl-tfagents/:/src/ tensorflow/tensorflow:2.4.0-gpu bash
    ```

## Other docker commands

1. List running Docker containers
    ```bash
    $ sudo docker container ls
    ```

1. Stop Docker container (replace CONTAINERID with ID of container to be stopped):
	```bash
    sudo docker stop <CONTAINERID>
    ```