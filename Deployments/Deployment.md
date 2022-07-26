A deployment can be created in many ways. One of them is, you can pull a docker image in your local machine and then in 
your manuscript you can specify the `spec.templete.spec.imagePullPolicy` as `IfNotPresent`. Then first it will search the 
container in our local machine if not present then will pull from remote. 
<br>

For this first you have to pull the image using

    $ docker pull imagename:tag
    $ kind load docker-image imagename:tag
kind load docker-image loads the image in our kind cluster.