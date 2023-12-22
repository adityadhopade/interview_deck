## The images present locally and not in the DockerHub and want to use locally in the minikube then you can make the use of the setup steps as follows

```
# Start the minikube
minikube start

# Set docker env
eval $(minikube docker-env)             # Unix shells

# Check if the images are present locally
docker image ls

# IF not present build it using Docker
docker build -t myapp:baseImage

# Run in Minikube
kubectl apply -f myapp-base-deployment.yaml

# Add the image pull policy set to Never; IfNotPresent
image-pull-policy=Never
```

---

# SEtting UP the HPA

######################################################################################

# Setup

######################################################################################

### Before running deployment make sure the images are persent in the system, use updated-image-with-metrics for building.

```
### deploy the production deployments
kubectl apply -f example-myapp-production/deployment.yaml

### deploy the production deployments svc
kubectl apply -f example-myapp-production/svc.yaml

### create nginx for testing
kubectl run nginx --image=nginx

### curl the myapp-production service from nginx prod
kubectl exec -it nginx bash
curl myapp-production-service.default.svc.cluster.local:80
```

## For setting up the HPA what we need is to have the following

## In order for HPA to work it nees a `KuberMetricsServer`

- It will take the image from the `adityadho/myapp:productionImage` from `DockerHub`
- Check if the hopa is present `k top nodes`
- If it is not installed it will pop not availble; we can install the `Kube- Metric Server`

```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

- if READY value is 0/1 for deployment.apps/metrics-server after running below command

```
kubectl get deploy -n kube-system
```

- then do following as per https://dev.to/docker/enable-kubernetes-metrics-server-on-docker-desktop-5434

```
wget https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

- add below arg at Deployment->spec->template->spec->args

```
--kubelet-insecure-tls
```

- HPA Comes as a Object to Kubernetes

```
kubectl api-resources

kubectl explain hpa --recursive

```

## What is HPA?

- It points to one deployument and keep monitoring the resources of deployment with the target percentages that we provide.
- Let us try rto dry run the HPA first

```
kubectl autoscale deployment myapp-production-deployment --cpu-percent=50 --min=1 --max=5 -o yaml --dry-run=client
```

- Dry run data will be as shown below

```
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  creationTimestamp: null
  name: myapp-production-deployment
spec:
  maxReplicas: 5
  minReplicas: 1
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp-production-deployment
  targetCPUUtilizationPercentage: 50
status:
  currentReplicas: 0
  desiredReplicas: 0
```

## Explaination of the HPA dry RUN !?

- Whenveer my CPU percentage remains 50%; autoscalerr should trigger for the `kind:Deployment` and for the deployment name `myapp-production-deployment` and if the POD's reaches the `CPUcapacity of 50%` we `trigger one more POD`
- It can `NOT only scale-up` ; it can `scale down also`

- Let us try and bring down the CPU Utilization to `30%` we can get it by changing the `--cpu-precent` field set to `--cpu-precent=30`

- BY default HPA provides the autoscaling using the triggers of `CPUUtilization` and `MemoryUtilization` we can use them both and in isolation as well.

- **[NOTE]** If we need some custom triggers then we need to define it on our own; so the parameters on which we are judging will be different but the process of Triggering will be the same.

- If we have an application and have certain endpoints; here we are managin the infrstruture; if we want to managed the application of the POD we need to know and monitor them using the `som objects`

- Service => Reaches to POD ==> Within POD we need to have a defined Endpoints to monitor ==> It cannot be like a piece of code taht is not exposed outside

```
#Changing the CPU utilization to 30%
kubectl autoscale deployment myapp-production-deployment --cpu-percent=30 --min=1 --max=5

# Checkout the POD's
kubectl get pods

# below command would only work if we have the metric server installed

k top pods ==> gets us the usage of the pods

```

- IT can even try to scale down if the CPUUtilization is not triggering the CPULIMITS provided in the HPA Autoscaler. `(It takes time to autoscale down, autoscale up)`

- If we try to get the HPA object it will give us the

```
NAME                          REFERENCE                                TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
myapp-production-deployment   Deployment/myapp-production-deployment   10%/30%   1         5         2          3m14s
```

## Generate the Load for HPA !?

- We can also start generating loads on it to test out the HPA working as per our expectaions or not?!

- The commands to add the load on the resource can be used as follows

```
kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "count=0; while sleep 0.01; count=$((count+1)); do wget -q -O- http://myapp-production-service.default.svc.cluster.local:80; echo "\n"; done"

## Chnage the name of the container form load-generator to load-generator1, load-generator2 etc (If runnning opn one or more terminals)
```

## Decipher the commmand usedf above

- `kubectl run`: Initiates the creation of a resource in the cluster.

- `i --tty`: Allocates an interactive terminal(pseudo terminal) and connects it to the pod.

- `load-generator` : The name of the pod being created.

- `--rm` : Deletes the pod upon termination. This makes it a one-time job; (can be used with the Container as well)

- `--image=busybox:1.28`: Specifies the Docker image to be used for the pod. In this case, it's BusyBox version 1.28.

- `--restart=Never`: Specifies that the pod should not be restarted automatically.

- `/bin/sh -c "count=0; while sleep 0.01; count=$((count+1)); do wget -q -O- http://myapp-production-service.default.svc.cluster.local:80; echo "\n"; done":` The command to run inside the pod. It's a shell script that uses a while loop to repeatedly make HTTP requests to the specified URL (http://myapp-production-service.default.svc.cluster.local:80). It increments a counter (count) and sleeps for 0.01 seconds between each request.

- We would get multiple pods running with the replicas now

```
NAME                          REFERENCE                                TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
myapp-production-deployment   Deployment/myapp-production-deployment   85%/30%   1         5         3          17m
```

- To keep a tab on the load generated we can make the use of `watch`

```
watch kubectl get hpa
```

- Observing it for some time it leads to the maximum capacity of the replication here we have set maximum=5; so it will go upto 5 `(Scaled to the maximum using Autoscaler)`
- The `minimum` number set will also override what is written within the deployment manifests.

```
aditya@aditya-Inspiron-3576:~/instructors/syed nadeem/project_sessions/session-6$ k get hpa
NAME                          REFERENCE                                TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
myapp-production-deployment   Deployment/myapp-production-deployment   22%/30%   1         5         5          22m
```

- let us try to Scale Down the Pods in the Autoscaler by `Stopping the load-generators`; The replicase will start to vanish in some time; it can go to the minimum setted to 1 `(De-scaling to the minimum using Autoscaler)`.

```
## checkout the pods
kubectl get pods
```

- This will take some time to `de-scaled`

```
aditya@aditya-Inspiron-3576:~/instructors/syed nadeem/project_sessions/session-6$ k get hpa
NAME                          REFERENCE                                TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
myapp-production-deployment   Deployment/myapp-production-deployment   10%/30%   1         5         2          33m
```

- `K8 has controller objects; HPA Comes inherently to the K8's as HPA is the native K8's Object.`

- **[NOTE]**: `In the autoscaling we need to setup a cap on the MAXPODS for the Infra-CostSavings`
- No notifcations for scaling up and scaling down
- They need to perform the `PerformanceTesting` and then only release after examining the `XTPS(Transactions Per Seconds)`

- Not recommended to use tghe HPA and VPA (Beef ME Up) together; System does not know what to do either scale horizontally or should uppgrade existing infra (beefing up the Infra).

- **[NOTE]** VPA is not a Native K8 Object; we need to install something like a K8 Operator for VPA.

### Q How to define/calculate `thresholds` like min and max of the application for HPA, TPS(Transaction per Second) etc?

- When start buiding the application we do not need to define the `MAXPODS` while developing the application.
- Once we do the Unit Testing and Functionality Testing then we move onto the Higher environments like QA STAGING(PRE-PROD) PROD
- We will define our thresholds there in Higher Environments like TPS(Transactions Per Second) some other parameters like that.
- In Promethues we monitor N/W latency, N/W throughput,Response and Request Time these are somethings we cannot alter/modify during the deployment but CPU and MEM we can change.
- There is something called as `Quality of Service`

- In the Producation Environment the request and Limits are set different (cpu limit=100 and cpu request =10) os genrally it is kept in the ratio of like `limit:request= 1:10`
- It can act as `Vertical Scaling` => In this case when a CPU request tries and gets towards CPU limit (100) in our case; it would try to vertically scale until the whole CPU limit is consumed.
- SO what we will do is we try to add a trigger for `Horizontal Pod Scaling` at before letting the CPU consume till `CPUlimit(100)`; Suppose we set it at `(60%)`; Whenever the CPU Request hits 60% and beyond it will Horizontally Scale and generate the replicas to distribute the load
- **[NOTE]** (The replicas generated will alos have the same CPU LIMITS and CPU REQUESTS as of Orignal One)
- **[NOTE]** SO if Replica's CPUUtilization also hits 60% then also it will regenrate a new replica for that.

### What is QOS(Quality Of Service) in Kubernetes?

- In Kubernetes, Quality of Service (QoS) refers to the categorization of pods based on the resource requirements and resource usage patterns.
- Kubernetes uses QoS classes to make decisions about how to prioritize and evict pods when the cluster is under resource pressure.

- **Guaranteed (QoS Class: Guaranteed):**

  - Pods in the Guaranteed QoS class have both memory (memory.request) and CPU (cpu.request) resource requests specified.
  - These pods are guaranteed to get the resources they request. They won't be evicted due to resource constraints unless they exceed their specified resource limits.
  - The resources reserved for these pods are exclusive, and they are not shared with other pods.

- **Burstable (QoS Class: Burstable):**

  - Pods in the Burstable QoS class have resource requests (memory.request and/or cpu.request) but no resource limits (memory.limit and/or cpu.limit) specified.
  - These pods can consume resources beyond their requests, up to their specified limits, if available. However, they may be subject to throttling if the node becomes resource-constrained.
  - Burstable pods share resources with other burstable and best-effort pods on the node.

- **Best-Effort (QoS Class: BestEffort):**

  - Pods in the Best-Effort QoS class have neither resource requests nor resource limits specified.
  - These pods get what is available on the node. They don't have guarantees for resources, and they are the first to be evicted if the node runs out of resources.

- The QoS classes are used by the Kubernetes scheduler and eviction manager to make decisions when the cluster is under resource pressure. For example, when a node is running out of resources, the scheduler may prioritize the eviction of best-effort pods first, then burstable pods, and finally, it will only evict guaranteed pods if necessary.

- Understanding and setting appropriate QoS for your pods is important for resource management and ensuring that critical workloads get the resources they need. It helps Kubernetes make intelligent decisions when there is contention for resources on a node.

---
