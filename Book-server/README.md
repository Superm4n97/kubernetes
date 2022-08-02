This server is created using kubectl and yamls.

### 1. Create Image
* Create a server
* Dockerized it

### 2. Create an Ingress Cluster
* Create new ingress cluster
* set up the nginx proxy
* Set up a DNS in your local machine:
```shell
sudo nano /etc/hosts
```
There you will see something like:
```shell
127.0.0.1       localhost
127.0.1.1       rasel-ubuntu
127.0.0.1       bookapi.localhost.test
```
these are your local dns proxies, you can think it as a key value pair. Where if you send request 
at `bookapi.localhost.test` then your ingress proxy will route the request to 
`127.0.0.1`. Your can edit these proxies as you want.<br>

If you setup the ingress cluster. Your ingress will be given an ip address. In my case I got the `ADDRESS` is `localhost` 
which is basically `127.0.0.1` so I set the host of `bookapi.localhost.test` is `127.0.0.1`.

### 2. Create a Deployment
* The deployment will contain one or more pod replicas

### 3. Create Service
* Service is the L4 load balancer. It manages the pods of the deployment.

### 4. Create Ingress

After setting up all 4 steps, you can access the pod in the url `bookapi.localhost.test/ping`.