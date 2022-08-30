### Exec pod
```shell
$ kubectl exec -ti POD_NAME INTERFACE
$ kc exec -ti kuard-config sh
```
`POD_NAME` is the name of the pod, and `INTERFACE` is the interface in which the pod will be viewed. An interface can be 
`sh` for shell, and `bash` for bash.

### Log of a pod
```shell
$ kubectl logs -f POD_NAME
$ kc logs -f kuard-config
```
The command shows the log information of the pod.

### All resources
```shell
$ kubectl api-resources
$ kubectl api-resources --namespaced=true
```
Gives you all the resources in your cluster. Enabling `--namespaced=true` flag will give all the resources that works within 
a namespace. 


### All versions
```shell
$ kubectl api-versions
```


### Get output in YAML
```shell
$ kubectl get pod POD_NAME -o yaml
```

It shows the yaml configuration of a pod. 

## kind
```shell
$ kind cluster
```

It will show all commands of kind. <br> If kind is not running then try this:
```shell
$ docker ps
```

see if the `kindest` image is running or not, then restart the kubectlst image as,
```shell
$ docker restart CONTAINER_ID_OR_NAME
$ docker restart 63a0c7828de2
```
* To delete a cluster:
```shell
$ kind delete cluster
```

*To create a cluster:
```shell
$ kind create cluster
```

## kubectl
### Get
```shell
$ kubectl get pods
$ kubectl get services
$ kubectl get deployments
$ kubectl get replicaset
$ kubectl get componentstatuses #gets the components of the cluster
$ kubectl get pod -o wide # shows more information
```

```shell
$ kubectl get deployment nginx-deployment -o yaml > nginx-deployment.yaml
```
Saves the deployment in nginx-deployment.yaml file. But this file
will have lots of data. You can use this command to debug a
deployment or create a copy of that. This command is also
applicable for pods and services.

```shell
$ kubectl get pods -w
```
It shows all the pods `-w` is used to watch.

```shell
$ watch kubectl get pods
```
It watches the pods in realtime.

### Selecting objects on some basis
```shell
$ kubectl get pods -l app=kuard,version=2
```
where -l stands for the labels and labels are separated by (,).

```shell
$ kubectl get pods --namespace=kube-system
```

### Create:
```shell
$ kubectl create -h
```

shows all the create commands, objects which can be created.

usages:
```shell
$ kubectl create -f FILENAME [options]
$ kubectl create deployment nginx-test --image=nginx
```

Here nginx-test is the deployment name and nginx is the container image name.
It is the most optimized format. You just have to give a deployment name and an
image that it will run.

### Edit
```shell
$ kubectl edit deployment nginx-test
```
nginx-edit is the deployment name

### Debug
```shell
$ kubectl logs [pod name]
$ kubectl logs nginx-test-847f5bc47c-v8lm8
```
It shows the log information of the container image that is running inside the
pod.

```shell
$ kubectl describe [obj]
$ kubectl describe pod
$ kubectl describe obj [obj_name]
$ kubectl describe nodes [node_name]
$ kubectl describe pod [pod_name]
```
gets all the information about a specific node.

```shell
$ kubectl exec -it [pod name]
```
with this command we can get into the terminal of that pod. <br>
-it stands for interactive terminal.

### Delete
```shell
$ kubectl delete deployment [deployment name]
```
It deletes the deployment along with the replicaset and pods.

```shell
$kubectl delete deployments --all
```
deletes all the deployments.

```shell
$ kubectl delete rs,svc,job -l chapter=jobs
```
This command deletes the replicaset, service and jobs where the label of that object
is `chapter=jobs`

### Apply
It's hard to write every information in the commandline. so we create a yaml
configuration file and apply it. If the object name is obj.yaml then we can write
```shell
$ kubectl apply -f obj.yaml
```
Creates an object from the yaml file,

```shell
$ kubectl delete -f obj.yaml
```
deletes an object

To know more about yaml deployment file check their [official repository](https://github.com/kubernetes/api/blob/master/core/v1/types.go)

### rollout
```shell
$ kubectl rollout status daemonSets my-daemon-set
```

Some rollout command can be found in the _Up and Running_ **Deployment Rollout** section.

### Context
context is used for changing the default namespace permanently.
```shell
$ kubectl config set-context my-context --namespace=mystuff
$ kubectl config use-context my-context
```
first line creates the new context, second line use the newly created
context.

# Database Create
1. Run mysql database
2. 
```shell
kubectl exec -ti test-sts-0 bash
```
to exec into the pod.<br>
Now you are in the pod in using bash.

3.
You can see the environment variables using the along with the username and pass
```shell
env
```
4.
you can login into mysql using 
```shell
mysql u root -p 
```
