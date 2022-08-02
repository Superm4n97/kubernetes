> $ kubectl logs [pod name]<br>

it shows the log information of a pod.

> $ kubectl logs -f [pod name]<br>

it shows the continuous log information of a pod.


> $ kubectl port-forward pod/[pod name] [local-machine port]:[pod port]<br>
> $ kubectl port-forward pod/book-server 8080:8080<br>

it forwards the every hit of our machine localhost:8080 to the book-server 8080 port.

> $ watch kubectl get pods<br>

keeps watching the pod's activity.

> $ kubectl apply -f [yaml file name]<br>

creates or updates the file.

> $ kubectl delete -f [yaml file name]<br>

deletes the object that is described in the yaml file. 