# INTERVIEW QUESTION WITH ANSWERS

### What are Containers?
- Containers are kind of advanced VMs 
- They are wrapped around the Application + Dependencies + System Binaries
- In the containers are lightweight as they do not contains the whole OS in it.
- It resolves the problem of "It runs on my machine" as it creates and environment for the Application with its required dependencies within.


### What if another person wants to use our application (running on our computer)?
- We would provider them with the "Docker Images"
- Docker Images will have the Application + Dependencies + System Binaries, and Docker Image's running instance is nothing but the Docker Container.

### What are the type dependencies which are included in the Containers?
- These are the run-time dependencies that are needed when running the applications
- There are also other types of Dependencies like Build Time Dependencies, and Compile Time Dependencies but Docker does not deal with it.

### What is docker damemon (dockerd) ?
- dockerd is responsible for building running and managing the containers

### What is a Cluster?
- A cluster is at least compromising of 2 major components they are 
  - Master Node <Control Plane>
  - Worker Node <Data Plane>
- Cluster can have multiple masters and workers also generally it comprises multiple master nodes; which are in the ratio master: worker set to 1:2.
- For every master node we need to have ideally 2 worker nodes for the high availability [H.A.]

### Features of the Containers?
- The containers provide isolation.
- Each container is provided with the help of namespaces and groups [control groups].
- namespaces => Provides the isolation
- groups => Provides manages the CPU and memory
- In the Docker, all this is internally handled we do not directly need to deal with this in a detailed extent.

### What if we have 10000's of Containers? How to manage them?
- It can be managed via the help of Orchestrators. Orchestarctors are the one who keeps the track of each container and maintains them according to our desired state.
- Orchestrator's only work is to manage the runtime of the containers; let it be 100 or 1 million it does matter to it it just needs to manage the runtime of containers.
- To manage the resources of the scaled instances also they can be used / portfolio management of resources in K8's.
- Some very famous orchestartors are Kubernetes[K8's] and Docker Swarm.

### What was before K8's then ?
- People used to use the Docker Swarm (technically it is used also now but in less favor) for Orchestration.
- Docker Swarm has some shortcomings like
  - Usable for simpler use cases; missing advanced orchestration features like auto-scaling, rolling updates, and support to stateful applications.
  - Docker swarm cannot be extended but K8's can with the help of CRD's [Custom Resource Definetions] and Operators
  - Docker swarm is losing the community adoption and getting replaced with the K8s. 


### What is the granular most level of object in K8's?
- Pod is the one granular level object that we can have control over.
- even every component is boiled down to the k8's object; Which is nothing but the Pods running for the each of Objects.
- It further boils down to Containers; Pods are nothing but a set of containers running.

- Summarised it as ANY K8's Object ==> PODS ==> Containers
- That is why Folk learning Containers is essential.

### Why generally don't go to learn the managed Cluster directly?
- In the managed clusters whole of the master Node <Control Plane> is just hidden and we are only given the control of the worker nodes.
-But it is essential to know the workings of the API Server and how it interacts with the other components of the master as well as the worker nodes.
- Some well-known managed services are AWS EKS and Azure AKS.

### How to determine that a Node is of type master or worker?
- Master Node(s) should have the components like 
  - **KUBE API SERVER** (THE MAIN COMPOENENT)
  - **ETCD** (KEY: Value Data Store; Stores the metadata of scheduling)
  - **Control Managers** (Determines Curr. vs Desired State; deals with components like Dep. ,RS, DS etc.)
  - **Scheduler** (Schedules the Pod according to the State of Cluster)
  - **Cloud Control Manager**[CCM] (Optional)

- Worker Node(s) should have the components like
  - Application+ Dependencies + Sys Binaries in Docker Images
  - Docker Containers made from Docker images
  - **Kube Proxy** (Networking Slave of Kube API server)
  - **Kubelet** (It is a Slave of the Kube API Server for managing the creating and managing Pods; Runs on each Node)
  - Container Network Interface [C.N.I] (Weavenet, Calico, Tiger)
  - Conatainer Runtime Interface [C.R.I] (runc, rkt, cri-o)

### Where does the external user interact with the Kubernetes?
- External users can only access the K8s via an Interface.
The external user's endpoint would be the KubeAPI Server. In which they can connect but it needs to be with the proper authentication.  

### What are the different interfaces through which we can connect to the K8's?
- kubectl (Most widely used for the managed services to connect to the Kubernetes Kube API Server)
- Browser
- Golang Binaries
- API's

### What is the Architectural flow of the K8's Architecture?

### What are the basic components in the VPC created by default?
- Public Subnet <Divide it into different availability zones>
- Private Subnet (Optional)
- Route Table
- Internet Gateway

### Benefits of Python
- Does not need to compile any code (It acts as both the High-level language and the low-level language) to interact with Kernel
- Python is an Interpreted language so no need to convert it to the low-level language
- In Java it needs to be converted to .class(acting as the low level language) to interact with Kernal
- The Docker File for the interpreted language will be different from that of the compiled code language.
- Python Docker File will not involve any build step in the Docker File
- In Java, I suppose we need to first have a build (.war,.jar,.ear file) and then use it in the Docker File.

### Docker Architecture

_ To build the Docker File 
- **Docker File** will be made with the instructions provided on Docker Client(Docker Client is nothing but the CLI)
  - 15-20 Instructions
  - Consists of Commands and Arguments 
  - Each line in the Docker File is considered a Layers
  - Docker image is nothing but the bundling of all the Layers in a bundle in the Docker File. 

  ## Line-by-Line Roles of Docker File
  - GO and Download the OS 
  - Follow the instructions Provided
  - Other Meta Layers
  - ENDPOINT - Last Line gives how to start the application (Known as **ENTRYPINT/CMD**)
  
  ## Flow of Docker File (Internal Details)
  - The image created would only be the **Read Only Object** (Nobody should be able to manipulate the image once created)
  - When the Docker Image is shared it should only care about the Endpoint as all other above layers are already inculcated(hardcoded) in the image.
  
  - When the image starts it creates a virtual environment that is called a **Docker Container**
  - Docker Container will contain all the context of the Docker File.
  - Docker Container will run the Entrypoint to create the files, perform operations, and expose the network according to network configurations.
  - **NOTE** : Docker Containers are also **READ ONLY** (Cannot manipulate a Docker Container) ; **If needed those kind of changes only can be done while creating the Docker File.** 
  - **NOTE** : Read ONLY also known as Ephemeral

## Whose responsibility is to write the DockerFile?
- It is the responsibility of the Developer and the Devops Engineer to write the Dockerfile
- They should work in the collabaration (insync) with each other.
- Generating Docker File is a manual effort although there are automation tools for developing the DockerFile(as projects are not mature enough)
- We can ask the chatgpt to identify the loopholes in the Docker Files.


  

## Prerequisite to Docker Architecture
![Alt text](image.png)  

## What is Docker Daemon?
- Docker Host actually consists of Docker Daemon and in turn **it manages the container runtime.**
- Docker Daemon can have the Docker Client running(as it's a CLI for Docker).
- All the containers and Images will run in the Docker Daemons.

- The Docker Client actually runs the commands; the actual flow starts in the daemon; the Client actually connects to the Daemons; all the containers and Images will run in the Docker Daemons.

- When hitting the docker command it will not run if only the docker client is installed it needs both the docker client and the docker daemon to be installed in it.

- Docker is not a replacement for anything; It is the concept of containerization

## Docker Internals for namespaces and Cgroups
![Alt text](image-1.png)

## TO check the logs of Docker?
- docker logs -f <container id>

## Some important docker commands?
- **docker container ls / docker container ps** - To show the running docker containers
- **docker containers ls -a / docker container ps -a** - To show all the containers (running and stopped)
- **docker build -t <Image Name to be created> /path/to/the/dockerfile**
- **docker pull nginx** - to pull the nginx image(it will not run this)
- **docker run -p 9000:9000 nginx** -To run the nginx image; mapping the PORT 9000 of the Host(LHS) to the PORT 9000 of the Container (RHS).
- **docker exec -it <container_id> bash** - To get inside the container and interact with it using the bash shell.
- **docker stop <container_id>** to stop the container 
- **docker rm <container_id>** only after stopping the container we can remove the container
- **docker rmi <image name>** only after removing all the containers associated with the docker image we can delete the docker image
- **docker system prune** -  will clean up any resources — images, containers, volumes, and networks — that are dangling (not tagged or associated with a container):
- **docker rmi $(docker images -a -q)** - To remove all the images
- **docker rm $(docker ps -a -q)** - To remove all the containers
- **docker rm $(docker ps -a -f status=exited -q)** - To remove all the containers with the status as exited.
- **docker volume prune** - TO remove all the volumes which are  dangling (not assosiated with anybody)
- **ocker rm -v container_name** - To remove a container and its volume

