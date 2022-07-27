### How to exec pod
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