# INDEX
* 0.Kubernetes Basics
  * Architecture
  * Cluster Component
  * Kubernetes Component
* 5.Pods
  * Health Check
  * Resource Management
* 9.ReplicaSets
  * Pod Auto Scaling
* 10.Deployment
  * Rollout
* 11.DaemonSet
* 12.Jobs
  * One Shot
  * Parallelism
  * Work Queues
  * CronJob
* 13.ConfigMaps and Secrets
* 14.Role Based Access Control(RBAC)


# 0. Kubernetes Basics

## Architecture

3 process must need to be installed on every node.
1. Container Runtime (Docker, Kubelet (manage containers) etc.)
2. Services. (Load balancing and communication)
3. Kube Proxy 

### Master Node
#### API Server
It is the center point of all sort of communication. Every request
comes into the api server, and it forwards the request into the 
responsible point.

#### etcd Cluster
It is the database of the system. It keeps the information about 
the containers, pods and other things.

#### Controller Manager
It checks whether actual_state = desired_state. It finds the 
information about the actual state form the etcd cluster through the api server.
It also sends the desired action that need to be taken to the api 
server. 

#### Kube Scheduler
It takes all the actions, it performs the specific task that 
has been told from the api server. It does not take any decision, 
it just performs the actions. 

### Worker Node
#### POD 
POD is the smallest unit of kubernetes. POD contains containers
inside it. A pod contains an ip address. A pod can have more than
one container, but the best practice is to keep a single container 
in a pod. The containers inside the pod are tightly coupled, so 
whenever a container fails all the container fails, and the pod is
killed. Again only the pods consist of ip address not the containers.

#### Kubelet
It manages the pods. Kubelet controls the pods also the containers
inside the pods. If a pod or a container needs to be created or 
deleted kubelet does it.

#### Kube Proxy
it provides ip addresses to the specific pods. When a new pod is 
created kube proxy provides a new ip address to that pod.


#### kubectl
kubectl uses for interacting with api server. The admin or user both
need to access api server to fulfill their purpose. 



## Cluster Component
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
```shell
$ kubectl proxy
```

It starts up a proxy server to access the UI. The server starts
on `localhost:8001`

## Kubernetes Component
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

# 5. Pods
## Health Check

### Liveliness Probe:
Liveliness probe is a segment of yaml code that continuously checks if the 
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

`request`
<span style="color:orange">
sets the lower bound of the resource that the application will consume, 
we can also set the higher bound of the resource by using `limits`. It will specify
 the maximum amount of resource that the pod will use.
</span>

by using `requests` and `limits` we can specify the resource interval of a 
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

# 13. ConfigMaps and Secrets

Configmap and secret store key pair literals. Where configmap used to store environment variables. Secrets are used to store 
authentication for a container. 

Creating a configMap:
```shell
$ kubectl create configmap my-config \
 --from-file=my-config.txt \
 --from-literal=extra-param=extra-value \
 --from-literal=another-param=another-value
```

Creating a secret:
```shell
$ kubectl create secret generic kuard-tls \
 --from-file=kuard.crt \
 --from-file=kuard.key
```

## Managing a configmaps and secrets
### Listing
### Create
### Update

# 14. Role-Base Access Control (RBAC) for Kubernetes
RBAC is kind of access management, that gives permission to individuals. There are some terminology in RBAC 
**identity**, **role bindings**, **control**.

## Identity in Kubernetes
Every request that comes to Kubernetes is associated with some identity. Kubernetes makes a distinction between user identities 
and service account identities. Service account identities are associated with components running inside the cluster.

## Roles and Role Bindings
Identity the first step of kubernetes authorization. Once the system knows the identity of the request then it need to 
determine if the request is authorized for that user. This is done by role and role bindings.
<br>

A role is a set of capabilities, capability of doing something.<br>
A role binding is an assignment of role to some identities. If a role is assign to somebody is will be capable of doing 
specific tasks.<br>

In kubernetes there are two pairs of related resources that represent roles and role bindings. One pair applies to just a 
namespace (Role and RoleBinding) while other pair applies across the cluster (ClusterRole and ClusterRoleBinding).<br>

Role resources are namespaced, and represent capabilities within that single namespace. You can not use namespaced roles 
for non-namespaced resources, and binding a Role Binding to a role only provides authorization within the Kubernetes namespace 
that contains both the Role and the RoleDefinition.<br>

**Example of a Role:**<br>
A role that gives an Identity the ability to create and modify Pods and Services.

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io.v1
metadata:
  namespace: default
  name: pod-and-services
rules:
- apiGroups: [""]
  resources: ["pods","services"]
  verbs: ["create","delete","get","list","patch","update","watch"]
```

**Example of a RoleBinding:**<br>
To bind the Role above to user alice, this role binding also binds the groups mydevs to same role,

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  namespace: default
  name: pods-and-services
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: alice
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: mydevs
role:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: pod-and-services
```
<br>

You can see all the build in role in kubernetes using `$ kubectl get clusterroles`.
Most of the built-in roles are for the system utilities, only four of them are generic end users:
* cluster-admin (cluster)
* admin (namespace)
* edit (namespace)
* view (namespace)

You can see the clusterRoleBindings using the command `$ kubectl get clusterrolebindings`, it will show all the bindings 
that are assign to some identities.<br><br>

There are several Kubernetes RBAC verbs

    Verb (HTTP method)
    ------------------
    create (POST)
    delete (DELETE)
    get (GET)
    list (GET)
    patch (PATCH)
    update (PUT)
    watch (GET)
    proxy (GET)

## Testing Authorization with can-i
Using built-in can-i command a user can check or validate their authorization in the cluster.
can-i command is simple to use and takes a verb and a resource:

```shell
$ kubectl auth can-i create pods
$ kubectl auth can-i get pods -l app=api-server 
```
This will return `yes` or `no` according to your authorization.

## Managing RBAC in Source Control
kubectl command-line tool comes with a reconcile command that operates somewhat like kubectl apply. It will reconcile our 
text based set of roles (rbac yaml config file) and role bindings with the current state of the cluster. 

```shell
$ kubectl auth reconcile -f some-rbac-config.yaml
```

It will reconcile the data of the file with the cluster. If you want to see the changes before they are made, you can add the 
`--dry-run` flag to the command to print but not submit the changes.

## Advanced Topics
### Aggregating Cluster Roles
### Using Groups for Binding