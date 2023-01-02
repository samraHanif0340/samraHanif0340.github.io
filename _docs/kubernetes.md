---
title: "Kubernetes"
---

+ Container orchestration system: automates container deployment, scaling, networking, storage, and scheduling.
+ `node`: A worker machine in the Kubernetes cluster, responsible for actually running pods
+ `pod`: layer of abstraction containing one or more container runtimes like Docker or rkt. Single-container pods are preferred.
+ If a machine within the cluster dies, affected container will be migrated to another machine. 
+ `kubectl`: A command-line utility with commands to control the Kubernetes cluster

+ Commands:
    ```bash
    $ docker build -t <container name> . #To build the container
    $ kubectl <command> <resource> <options> #General command format
    $ kubectl get pods <name of resource> #Retrieve information about a pod
    $ kubectl get all #Summary of each resource in cluster
    $ kubectl describe all #Provides detailed information
    $ kubectl apply -f <file name>.yml #To create, delete, or replace state of Kubernetes cluster
    $ kubectl delete -f <file name>.yml #To delete the object declared by the YAML file 
    ```
+ Kubernetes object Kind: Pod, ConfigMap, Deployment, Service

## Kubernetes Deployment
+ Deployment manages, replicates to handle increased load, and updates, a set of pods through a single parent object
+ Nests a pod specification (deployment.spec.template) inside the deployment's configuration 
+ A representative Kubernetes Deployment file is shown below.
    ```yml
    # deployment.yml
    apiVersion: extensions/v1beta1 # Kubernetes API version it’s from
    kind: Deployment # Type of object

    metadata: # Descriptive information of object
    name: tfsimagenet
    annotations: # Won’t be indexed or queried on
        contact: Marib Sultan <i.maribsultan@email.com> 

    spec: # Desired state of object
    replicas: 2 # How many copies of each pod do we want?

    strategy: # How do we want to update the pods?
        type: RollingUpdate # Updates pods one at a time
        rollingUpdate:
        maxSurge: 1 # Maximum number of pods above desired replicas

    selector: # Which pods are managed by this deployment?
        matchLabels: # This matches against the labels we set on the pod
        app: tfsimagenet

    template:
        metadata:
        name: tfsimagenet
        labels: # Used in deployment and service selector  
            app: tfsimagenet
        spec:
        containers:
            - name: tfsimagenet
            image: tfsimagenet
            imagePullPolicy: IfNotPresent
            ports:
                - name: rest
                containerPort: 8501
            resources:
    ```

+ `Horizontal Pod Autoscaler` is a new resource in modern versions of Kubernetes that manages the number of replicas in your deployment automatically, based on resource utilization(e.g., memory, CPU, custom metrics).

![podAutoscaler](/assets/images/wiki/wiki_kubernetes_03.jpg){:height="40%" width="40%" .align-center}

## Kubernetes Service
+ Services routes network requests to appropriate pods based on matching labels
+ A representative `Kubernetes Service` file is shown below.
    ```yml
    # service.yml
    apiVersion: v1
    kind: Service

    metadata:
    name: tfsimagenet-service

    spec:
    type: NodePort

    selector:
        app: tfsimagenet

    ports: # Three types of ports for a service 
        # nodePort - static  port on each node which is accessible from outside the cluster 
        # port - port exposed internally in the cluster 
        # targetPort - the container port to send requests to
        - nodeport: 30163
        port: 8080
        targetPort: 8501
    ```

+ The `Kubernetes Service` in the above file is illustrated pictorially below.

    [![service](/assets/images/wiki/wiki_kubernetes_04.jpg)](/assets/images/wiki/wiki_kubernetes_04.jpg)
