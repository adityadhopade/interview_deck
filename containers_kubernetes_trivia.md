# INTERVIEW QUESTION WITH ANSWERS

### What are Containers ?
- Containers are kind of advanced VM's 
- They are wrapped around the Application + Dependencies + System Binaries
- In the containers are light weighted as they do not contains the whole OS in it.
- It resolves the problem of "It runs on my machine" as it creates and environment for the Application with its reqired dependencies within.


### What if other person wants to use our application (running on our computer) ?
- We would provider them with the "Docker Images"
- Docker Images will have the Application + Dependencies + System Binaries; and Docker Image's runiing instance is nothing but the Docker Container.

### What are the type dependecies which are included in the Containers ?
- These are the run time dependencies which are needed when runing the applications
- There are alos other types of Dependencies like Build Time Dependencies, Compile Time Dependecies but Docker does not deasl with it.

### What is docker damemon (dockerd) ?
- dockerd is responsible for building running and mananging the containers

### What is a Cluster ?
- A cluster is atleast compromising of 2 major components they are 
  - Master Node <Control Plane>
  - Worker Node <Data Plane>
- Cluster can have multiple masters and workers also generally it compomises of the multiple master nodes; which are in the ratio master:worker set to 1:2.
- For every master node we need to have ideally 2 worker nodes for the high avalability [H.A.]

### Features of the Containers ?
- The containers provides isolation.
- Each container is provided with the help of namespaces and cgroups [control groups].
- namespaces => Provides the isolation
- cgroups => Provides manages the CPU and memory
- In the Docker all this is internally handeled we do not directly need to deal with this in detailed extent.

### What if we have 10000's of Containers ? Hiow to mananged them ?
- It can be managed via the help of Orchestrators. Orchestarctors are the one who keeps the tracks of each containers and maintains them according to our desired state.
- Orchastrators only work is to manage runtime of the containers; let it be 100 or 1 million it doesnt maer to it it juts only needs to manage the runtime of containers.
- To manage the reosurcess of the scaled instances also they can be used / portfolio management of resources in K8's.
- Some very famous orchestartors are Kubernetes[K8's] and Docker Swarm.

### What was before K8's then ?
- People used to use the Docker Swarm (techincally it is used alos now but in less favour) for Orchestration.
- Docker Swarm has some shortcomings like
  - Usable for simpler usecases; missing advance orchestration feratures like auto-scaling, rolling updates, supoport to stateful applications.
  - Docker swarm cannot be extended but K8's can with the help of CRD's [Custom Resource Definetions] and Operators
  - Docker swarm is loosing the commiunity adoption and getting replaced with the K8's. 


### What is granular most level of object in K8's ?
- Pod is the one granular most level object that we can have conrtrol over.
- even every component is boiled doen to the k8's object ; Which are nothing but the Pods running for the each of Objects.
- It further boild down to Containers; Pods are nothing but the set of containers running.

- Summarised it as ANY K8's Object ==> PODS ==> Containers
- That is why Folks learnming Containers is essentential.

### Why generally dont go to learn the managed Cluster directly?
- In the manged clusters whole of the master Node <Control Plane> is just hidden and we are onkly given the control of the worker nodes.
-But it is essential to know the working of the API Server and how it interacts with the other compoenents of master as well as the worker nodes.
- Some well known managed services are AWS EKS, Azrue AKS.

### How to determine that a Node is of type master or worker?
- Master Node(s) should have the components like 
  - **KUBE API SERVER** (THE MAIN COMPOENENT)
  - **ETCD** (KEY: Value Data Store; Stores the meta data of scheduling)
  - **Control Managers** (Determines Curr. vs Desired State; deals with components like Dep. ,RS, DS etc.)
  - **Scheduler** (Schedules the Pod according to State of Cluster)
  - **Cloud Control Manager**[CCM] (Optional)

- Worker Node(s) should have the components like
  - Application+ Dependecies + Sys Binaries in Docker Images
  - Docker Containers made from Docker images
  - **Kube Proxy** (Networking Slave of KubeAPI server)
  - **Kubelet** (It is Slave of Kube API Server for managing the creating and managing Pods; Runs on each Node)
  - Container Network Interface [C.N.I] (Weavenet, Calico, Tiger)
  - Conatainer Runtime Interface [C.R.I] (runc, rkt, cri-o)

### Where does the external user interact with the Kubernetes ?
- External user can only access the K8's via a Interface only.
- External user's endpoint would be the KubeAPI Server. In which they can connect but it needs to be with the proper authentication.  

### What aer different interfaces through which we can connect to the K8's ?
- kubectl (Most widely used for the managed services to connect to the Kubernetes Kube API Server)
- Browser
- Golang Binaries
- API's

### What are the Architectural flow of the K8's Architecture?

### What are the basic components in the VPC created by default?
- Public Subnet
- Private Subnet (Optional)
- Route Table
- Internet Gateway