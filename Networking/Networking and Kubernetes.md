# OSI and TCP models
# Networking in Linux
### Netfilter
![netfilter hooks](netfilter.png)
There are mainly five types of hooks
1. NF_IP_PRE_ROUTING
2. NF_IP_LOCAL_IN
3. NP_IP_FORWARD
4. NF_IP_LOCAL_OUT
5. NF_IP_POST_ROUTING

### iptables
iptables can be used to create firewalls and audit logs, mutate and reroute 
packets, and even implement crude connection fan-out. iptables uses Netfilter. 
Netfilter can intercept (stop) and mutate (change) packets.<br>
iptables components
> tables -> chains -> rules

table contains chains, chain contains rules.<br>
There are five types of tables 
1. Filter - for firewall related rules
2. NAT - for NAT related rules
3. Mangle - for non-NAT packet-mutating rules
4. Raw
5. Security

There are mainly five channels one for each Netfilter hook.
1. PREROUTING
2. INPUT
3. NAT
4. OUTPUT
5. POSTROUTING

5. <br>
When a packet passes through the channel a set of rules applied sequentially. 
Until the packet matches a "terminating target" (DROP) or reaches the end of the 
chain. 

### IPVS:
IP Virtual Server is a Linux connection load balancer. IPVS support multiple 
load balancing mode in kubernetes.
![IPVS modes](ipvs%20load%20balancing.png)

 You van create load balancers by running:

> $ ipvsadm -A -t <'address'> -s <'mode'>  

    This Chapter contains:
    * The Network Interface
    * The Bridge Interface
    * Packet Handeling in karnel
        * netfilter
        * Connreack
        * Routing
    * High Level Routing
        * iptables
        * IPVS
        * eBPF
    * Network Troubleshooting Tools
        * Security Warning
        * ping
        * traceroute
        * dig
        * telnet
        * nmap
        * netstat
        * netcat
        * Openssl
        * cURL

# 3. Container Networking Basics
    This Chapter contains:
    * Introduction to container
        * Applicaion
        * Hypervisor
        * Containers
    * Container Premitives
        * Control Groups
        * Namespaces
    * Container Network Basics
        * Docker Networking model
        * Overlay Networking
        * Container Network Interface
    * Container Connectivity
        * Container to Container
        * Container to Container Separate Hosts
## Introduction
### Hypervisor
Hypervisor is a way to increase one host machine's efficiency and remove 
the one operating system and networking stack issue. Hypervisor replicate 
hardware resources (CPU, Memory) to create guest operating system or virtual 
machines.<br>
#### Container:
A running container image
#### Image:
A file that creates container
#### Container Engine:
A system that accepts command line options to pull container image and run 
container. ex. Docker
#### Container Runtime:
A low level piece of software in a container engine that deals with running a 
container.
#### Base Image:
Starting point of a container image.
#### Image layer:
Image layer represent changes between itself and it's parent layer.
#### Image format:
A container engine have their own container format. such as Docker
#### Registry:
A remote server that stores images such as [Docker Hub](https://hub.docker.com/)
#### Repositories:
Layers of container images. 
#### Tag:
User defined name that represent the version of an image. 
#### Container Host:
The system that runes the container image. Ex: Local machine

### Container Functionality
Low level functionality:
    
    1. Creating containers
    2. Running containers

High level functionality:

    1. Formatting container image
    2. Building container image
    3. Managing container image
    4. Managing instances of conatainer
    5. Sharing container image

## Container Primitives
### RunC
No matter if you are using Docker or Containerd, RunC create and manages the actual 
container for them. Each of our container has Linux primitives called _control 
groups_ and _namespace_. 

### Control Groups
Cgroups control access to resources in the kernel for our container.

### Namespace
Namespaces are features of linux kernel that isolate and virtualize system 
resources of a collection of process. Such as, our main kernel runs process 
with unique process id's. A container has its own process with own PIDs. Docker 
bipasses these process to our kernel with other different PIDs. Docker runs the 
processes of a container in the Host OS. <br><br>

Bridge creates virtual ethernet (VETH) to connect node ethernet and container 
ethernet.
<br> 
### Container Networking Modes
It defines how a container connected to the host.
<br>**Bridge:** Default, creates a private network for container for communicate 
with the host.
<br>**Host:** The container shares the same IP address of the host. 
<br>
some other modes are: **None, Macvlan, IPvlan, Overlay, Custom.**

    When a docker starts, it creates a virtual bridge interface (docker0),
    on the host machine and assigns packets it a random ip address from the 
    private 1918 range. This bridge passes packets between two connected device.
    Each new container gets one interface automatically attached to the 
    'docker0' bridge.