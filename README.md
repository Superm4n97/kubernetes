# Kubernetes

## ARCHITECTURE
## Master Node
### - API Server
It is the center point of all sort of communication. Every request
comes into the api server, and it forwards the request into the 
responsible point.

### - etcd Cluster
It is the database of the system. It keeps the information about 
the containers, pods and other things.

### - Controller Manager
It checks whether actual_state = desired_state. It finds the 
information about the actual state form the etcd cluster through the api server.
It also sends the desired action that need to be taken to the api 
server. 

### - Kube Scheduler
It takes all the actions, it performs the specific task that 
has been told from the api server. It does not take any decision, 
it just performs the actions. 

## Worker Node
### - POD 
POD is the smallest unit of kubernetes. POD contains containers
inside it. A pod contains an ip address. A pod can have more than
one container, but the best practice is to keep a single container 
in a pod. The containers inside the pod are tightly coupled, so 
whenever a container fails all the container fails, and the pod is
killed. Again only the pods consist of ip address not the containers.

### - Kubelet
It manages the pods. Kubelet controls the pods also the containers
inside the pods. If a pod or a container needs to be created or 
deleted kubelet does it.

### - Kube Proxy
it provides ip addresses to the specific pods. When a new pod is 
created kube proxy provides a new ip address to that pod.


## kubectl
kubectl uses for interacting with api server


>$ kubectl get componentstatuses

gets the components of the cluster

>$ kubectl get nodes

gets the basic information about nodes, and list up the nodes

>$ kubectl describe nodes _node_name_

gets all the information about a specific node. 
## Cluster Components
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

