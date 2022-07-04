# Kubernetes

## 1. ARCHITECTURE

3 process must need to be installed on every node.
1. Container Runtime (Docker, Kubelet (manage containers) etc.)
2. Services. (Load balancing and communication)
3. Kube Proxy 

## 1.1 Master Node
### 1.1.1 - API Server
It is the center point of all sort of communication. Every request
comes into the api server, and it forwards the request into the 
responsible point.

### 1.1.2 - etcd Cluster
It is the database of the system. It keeps the information about 
the containers, pods and other things.

### 1.1.3 - Controller Manager
It checks whether actual_state = desired_state. It finds the 
information about the actual state form the etcd cluster through the api server.
It also sends the desired action that need to be taken to the api 
server. 

### 1.1.4 - Kube Scheduler
It takes all the actions, it performs the specific task that 
has been told from the api server. It does not take any decision, 
it just performs the actions. 

## 1.2 Worker Node
### 1.2.1 - POD 
POD is the smallest unit of kubernetes. POD contains containers
inside it. A pod contains an ip address. A pod can have more than
one container, but the best practice is to keep a single container 
in a pod. The containers inside the pod are tightly coupled, so 
whenever a container fails all the container fails, and the pod is
killed. Again only the pods consist of ip address not the containers.

### 1.2.2 - Kubelet
It manages the pods. Kubelet controls the pods also the containers
inside the pods. If a pod or a container needs to be created or 
deleted kubelet does it.

### 1.2.3 - Kube Proxy
it provides ip addresses to the specific pods. When a new pod is 
created kube proxy provides a new ip address to that pod.


## 1.3 kubectl
kubectl uses for interacting with api server. The admin or user both
need to access api server to fulfill their purpose. 

### Context
context is used for changing the default namespace permanently. 

> $ kubectl config set-context my-context --namespace=mystuff <br>
> $ kubectl config use-context my-context

first line creates the new context, second line use the newly created
context.

## Basic Commands

### kind
> $ kind cluster

It will show all commands of kind. <br> If kind is not running then try this:
>$ docker ps<br>

see if the kubectlst image is running or not, then restart the kubectlst image as,
>$ docker restart [63a0c7828de2 - container id/name]


> $ kind delete cluster <br>
> $ kind create cluster <br>

### Get
> $ kubectl get pods <br>

> $ kubectl get services <br>

> $ kubectl get deployments<br>

> $ kubectl get replicaset<br>

### Create:
> $ kubectl create -h<br>

shows all the create commands, objects which can be created.

usages:
> $ kubectl create -f FILENAME [options] <br>

> $ kubectl create deployment nginx-test --image=nginx  <br>

Here nginx-test is the deployment name and nginx is the container image name.
It is the most optimized format. You just have to give a deployment name and an 
image that it will run. 

### Edit
> $ kubectl edit deployment nginx-test <br>

nginx-edit is the deployment name

### Debug
> $ kubectl logs [pod name]

> $ kubectl logs nginx-test-847f5bc47c-v8lm8<br>

it shows the log information of the container image that is running inside the 
pod. 

>$ kubectl describe [obj]<br>
>$ kubectl describe pod


> $ kubectl describe obj [obj_name]

>$ kubectl describe nodes [node_name]<br>
>$ kubectl describe pod [pod_name]

gets all the information about a specific node.

>$ kubectl exec -it [pod name]

with this command we can get into the terminal of that pod. <br>
-it stands for interactive terminal.


### Delete
> $ kubectl delete deployment [deployment name]

It deletes the deployment along with the replicaset and pods.

### Apply
It's hard to write every information in the commandline. so we create a yaml 
configuration file and apply it. If the oject name is obj.yaml then we can write
> $ kubectl apply -f obj.yaml <br>

Creates an object, 

> $ kubectl delete -f obj.yaml

deletes an object

>$ kubectl get componentstatuses

gets the components of the cluster

>$ kubectl get nodes

gets the basic information about nodes, and list up the nodes


## CLUSTER COMPONENTS
### Kubernetes Proxy
kubernetes proxy is responsible for routing network traffic to
load-balanced service in the cluster. For load balancing 
proxy must be present on every node. If the cluster runs the 
Kubernetes proxy with Daemonset, we can inspect the proxy as:
>$ kubectl get daemonSets --namespace=kube-system kube-proxy

### Kubernetes DNS
DNS server is like a dictionary of a cluster, it stores the
information of naming and location of a specific service. <br>
It provides naming and discovery for the service that are defined
in the cluster. Depending on the size of the cluster there could 
be one or more DNS servers. <br>

### Kubernetes UI
> $ kubectl proxy

It starts up a proxy server to access the UI. The server starts 
on _localhost:8001_

## KUBERNETES COMPONENTS
### POD
POD is the basic component of kubernetes. POD consists of containers inside it.
Usually we put a single container inside a POD. POD gets an internal ip address. POD's 
communicate among themselves with that ip address.

### Service
POD's dies rapidly, so when a new pod is created a new ip should need to assign 
along it which is pretty inconvenient. Here comes the concept of services. Service is
a static ip address that is attached along each pod. Lifecycles of a service and pod 
are not connected, so when a pod dies service remains along with it's ip address. 
 Pods communicate with each other with service.<br>

Service has 2 functionalities:<br>
1. Permanent IP address
2. Load balancer
### Ingress
Service is divided into two categories, Internal and External. User request first 
goes to ingress then the request is forwarded according to the service. 

### ConfigMap
ConfigMap contains some mapped information. Suppose we need to access a pod using 
its service, if we change the name of the service then we again need to push and 
pull that pod. Instead of this we can use a generic configuration mapping that we do 
not need to change the whole thing. 

### Secret
Secret is also a map, but the values in secret is stored in base64 encoded.
Secret is used to store the credentials and authentication information. 

### Volume
When a container is declared, if it stores data, when the container will restart 
everything will be gone. so to store data permanently we use volumes along the 
pods to store data. The volume is mounted by the developer to some external local 
storage. 

### Deployment
Suppose a single pod application, when the application dies m the user will 
have a downtime of a server which is a bad user experience. <br>
Here comes the main advantage of the distribution system. In the distribution system we 
can have many replicas of a single application. In kubernetes to create another application 
replica we do not need to create another node. Instead of that we can make a 
blueprint and specify there how many copy we want. This blueprint is called 
deployment. So deployment is a script, using which we can create as many application
replica as we want. So we basically create deployment and deployment will create pods.
>deployment -> pods -> container

### StatefulSet
Multiple deployments can not be created for stateful set like database, because 
replica pods access volume concurrently. This is why database type of pods  
need to be created by statefulSet instead of deploy. 

