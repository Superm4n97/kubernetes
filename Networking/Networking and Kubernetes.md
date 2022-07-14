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