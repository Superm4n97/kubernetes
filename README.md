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

# Basic Commands

## kind
> $ kind cluster

It will show all commands of kind. <br> If kind is not running then try this:
>$ docker ps<br>

see if the kubectlst image is running or not, then restart the kubectlst image as,
>$ docker restart [63a0c7828de2 - container id/name]


> $ kind delete cluster <br>
> $ kind create cluster <br>

## kubectl
### Get
> $ kubectl get pods <br>
> $ kubectl get services <br>
> $ kubectl get deployments<br>
> $ kubectl get replicaset<br>
>$ kubectl get componentstatuses [gets the components of the cluster]

>$ kubectl get pod -o wide

gets more information than just get pod. 

>$ kubectl get deployment nginx-deployment -o yaml > nginx-deployment.yaml

saves the deployment in nginx-deployment.yaml file. But this file 
will have lots of data. You can use this command to debug a 
deployment or create a copy of that. This command is also 
applicable for pods and services.

> $ kubectl get pods -w
> $ watch kubectl get pods

for watching the pods realtime.

### Selecting objects on some basis
>$ kubectl get pods -l app=kuard,version=2 <br>

where -l stands for the labels and labels are separated by (,).
> $ kubectl get pods --namespace=kube-system

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

> $kubectl delete deployments --all

deletes all the deployments.

>$ kubectl delete rs,svc,job -l chapter=jobs

This command deletes the replicaset, service and jobs where the label of that object 
is `chapter=jobs`

### Apply
It's hard to write every information in the commandline. so we create a yaml 
configuration file and apply it. If the object name is obj.yaml then we can write
> $ kubectl apply -f obj.yaml <br>

Creates an object from the yaml file, 

> $ kubectl delete -f obj.yaml

To know more about yaml deployment file check their [official repository](https://github.com/kubernetes/api/blob/master/core/v1/types.go)

deletes an object

### rollout
> $ kubectl rollout status daemonSets my-daemon-set

Some rollout command can be found in the _Up and Running_ **Deployment Rollout** section.

# Health Check
## Liveness Probe:
Liveness probe is a segment of yaml code that continuously checks if the 
server container is live or not. It should be written for each of the object separately.
#### why it is used? 
Kubernetes only checks if the server is running or not, if not then kubernetes 
restarts it. But what if the internal program of a container caused deadlocks?
the server is still running, but it is not workable. That is why we use health 
checks. Liveness is one kind of health checks.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kuard
spec:
  containers:
    - image: gcr.io/kuar-demo/kuard-amd64:blue
        name: kuard
        livenessProbe:
          httpGet:
            path: /healthy
            port: 8080
          initialDelaySeconds: 5
          timeoutSeconds: 1
          periodSeconds: 10
          failureThreshold: 3
        ports:
          - containerPort: 8080
            name: http
            protocol: TCP
 ```

#### Readiness Probe:
It checks if the server is ready to serve the client or not. 

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

## Resource Management
### Resource Requests and Limits:
It specifies how much resource a pod will consume. 
```yaml
apiVersion: v1
kind: Pod
metadata:
 name: kuard
spec:
 containers:
   - image: gcr.io/kuar-demo/kuard-amd64:blue
     name: kuard
     resources:
       requests:
         cpu: "500m"
         memory: "128Mi"
       limits:
        cpu: "1000m"
        memory: "256Mi"
     ports:
        - containerPort: 8080
          name: http
          protocol: TCP
```

Suppose that we create a Pod with this container that requests 0.5 CPU. Kubernetes
schedules this Pod onto a machine with a total of 2 CPU cores.
As long as it is the only Pod on the machine, it will consume all 2.0 of 
the available cores, despite only requesting 0.5 CPU. <br>
If a second Pod with the same container and the same request of 0.5 CPU lands on
the machine, then each Pod will receive 1.0 cores.<br>
If a third identical Pod is scheduled, each Pod will receive 0.66 cores.<br>
Finally, if a fourth identical Pod is scheduled, each Pod will receive the 0.5 core it requested, and
the node will be at capacity.<br><br>

**_request_ sets the lower bound of the resource that the application will consume, 
we can also set the higher bound of the resource by using _limits_. It will specify
 the maximum amount of resource that the pod will use** <br>

by using _requests_ and _limits_ we can specify the resource interval of a 
pod.
### Volume Mount:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kuard
spec:
  volumes:
    - name: "kuard-data"
      hostPath:
        path: "/var/lib/kuard"
  containers:
    - image: gcr.io/kuar-demo/kuard-amd64:blue
      name: kuard
      volumeMounts:
        - mountPath: "/data"
          name: "kuard-data"
      ports:
        - containerPort: 8080
          name: http
          protocol: TCP
```

# 9. ReplicaSets
## ReplicaSet
It creates a set of working pods, and continuously checks whether the current number of pods 
matching the desired number of pods.<br>

Minimum replica set definition:

```yaml
apiVersion: extension/v1/beta1
kind: ReplicaSet
metadata:
  name: test-rs
spec:
  replicas: 1
  template:
    metadata: 
      labels: 
        app: test-app
        version: "2"
    spec:
      containers:
       -  name: app-server
          image: "dockerset.com/superm4n/apiserver.xyz"
```
to scale up or down the replicas you can update the configuration file spec section. For the command line update:
`$ kubectl scale replicasets test-rs --replicas=4`

## Pod Auto Scaling
There are two types of pod auto-scaling,
* Horizontal Auto Scaling (HPA):<br>
In horizontal auto-scaling the pod changes it replicas. The number of replicas affected in horizontal autoScaling.
* Vertical Auto Scaling:<br>
In vertical auto-scaling the pod resource are affected. It changes the CPU/Memory consume by the pod.

There are another type of autoscaling: Cluster autoscaling, which scales the cluster depending on the workload of the cluster.

### Horizontal Pod Autoscaling (HPA):
>$ kubectl autoscale rs test-rs --min=2 --max=5 --cpu-percent=80

This command auto-scale the test-rs pods between 2 and 5, depending on the threshold value of 
80% CPU. If a pod contain more than 80% CPU, then it will increase another pod.


# 10. Deployments

```yaml
apiversion: extensions/v1beta1
kind: Deployment
metadata: 
  name: test-dept
spec:
  selector:
    matchLabels:
      run: test-rs
  replicas: 1
  template:
    metadata: 
      labels:
        run: test-pod
    spec:
      containers:
      - name: test-container
-       image: imageurl.xyz
```

Deployment manage ReplicaSet which manages pods.
## Rollout
After you update the deployment it will trigger a rollout, which you can then monitor via the kubectl `rollout` command.

>$ kubectl rollout status deployments test-deploy <br>
>$ kubectl rollout pause deployments test-deploy [you can pause the rollout]<br>
>$ kubectl rollout resume deployments test-deploy <br>

You can see the deployment history by running:

>$ kubectl rollout history deployment test-deploy

    deployment.extensions/kuard
    REVISION CHANGE-CAUSE
    1          <none>
    2          Update to green kuard
The revision is given in oldest to newest. We can view the details about particular revision:
>$ kubectl rollout history deployment test-deploy --revision=2

You can undo the last rollout:
>$ kubectl rollout undo deployments test-deploy

If before rollout the history is:

    deployment.extensions/kuard
    REVISION CHANGE-CAUSE
    1          <none>
    2          Update to green kuard
    3          Update to blue kuard

Then after undoing the history will be:

    deployment.extensions/kuard
    REVISION CHANGE-CAUSE
    1          <none>
    3          Update to blue kuard
    4          Update to green kuard

Here revision 2 is gone and appeared as revision 4. If we undo it again, then revision 3 
will appear as revision 5. We can set the history limit upto certain number in the 
spec.revisionHistoryLimit = 14

## Deployment Strategies
### Recreate Strategy
### RollingUpdate Strategy
## Monitoring a Deployment
It monitors whether a deployment is dead or not.
spec.progressDeadlineSecond sets the time that how long the progress will be dead. 

# 11. DaemonSets
ReplicaSet should be used when your application is completely decoupled from the node, and you can 
run multiple copies on a single given node without special consideration.
<br>
DaemonSet should be used when a single copy of your application must run all or a subset of the nodes in the cluster.
<br>

By default, a DaemonSet will create a copy of a pod on every node unless a node selector is used, which will limit the 
eligible nodes to those with a matching set labels. DaemonSet determines which node a Pod will run on at Pod creation 
time by specifying the nodeName field in the Pod spec. As a result, Pods created by DaemonSet are ignored by the Kubernetes scheduler.

## Node Selector
If spec.nodeSelector is applied to DaemonSet then the pods will be applied only those selected nodes.
In the DaemonSet spec.nodeSelector section we have to use the labels of the nodes. If the labels of that node changed 
somehow then the DaemonSet will no longer be applicable to that node. Similarly if we create new node with that 
node label then the DaemonSet will be applicable to the new node also. 

## Rollout DaemonSet
## Delete DaemonSet

# 12. Jobs
Jobs creates a pod that performs a task once. Regular pods keeps restarting in a loop, but a job creates a pod perform 
a task and exit the pod. By default, a single job creates and runs a single pod. If a pod is fails before a successful 
termination then the job will recreate the pod with pod template in the job specification.  

## One Shot
A single pod is created using a single job and perform once. If the pod fails a successfull termination, the job recreates the 
pod until successfull termination. <br>
After the completion of the job, the related pod and jobs are still there for log analysis. 
The `$ kubectl get jobs` command will not show these completed jobs unless you pass flag `-a`. Delete the jobs before continuing.
> $ kubectl delete jobs oneshot

## Parallelism
Multiple pods can be created and the can work in parallel. spec.parallelism define a number 
representing the total number of parallel pods and spec.completions defines the number of pods that will be run 
parallel

## Work Queues

## CronJob
It schedules a job to run after a certain period of time. The declaration of a CronJob looks like:

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: example-crone
spec:
  #run every fifth hour
  schedule: "0 */5 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: batch-job
            image: my-batch-image
          restartPolicy: onFailure
```
Here `spec.schedule` contains the interval for the CronJob in standard cron format.