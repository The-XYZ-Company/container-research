## Docker Bridge Networking 

### Checking default docker bridge networks 

```
[root@docker-testing ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
bc9cbbbb0a5d   bridge    bridge    local
54e11a648da2   host      host      local
c9a5f9b78e3c   none      null      local
```

### Inspecting Bridge 

```
[root@docker-testing ~]# docker network inspect bridge 
[
    {
        "Name": "bridge",
        "Id": "bc9cbbbb0a5d232e933ab96df23ec8fb5a28a7f81c50d0db5eba05c4b2ddd5bb",
        "Created": "2023-02-09T07:48:09.987472835Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
```

### creating container and verify communication and network details 

```
[root@docker-testing ~]# docker  run -itd --name ashuc1 alpine 
59f1c0fb301728f452ae292fec28801bf45fa5c7c891ac3dcf3791d3b208a8e8
[root@docker-testing ~]# docker  run -itd --name ashuc2 alpine 
237bddde318da0f55c599a74eac3b19165506ee12523cab15bd4b455f13ea422
[root@docker-testing ~]# docker  ps
CONTAINER ID   IMAGE     COMMAND     CREATED         STATUS         PORTS     NAMES
237bddde318d   alpine    "/bin/sh"   2 seconds ago   Up 1 second              ashuc2
59f1c0fb3017   alpine    "/bin/sh"   9 seconds ago   Up 8 seconds             ashuc1
[root@docker-testing ~]# 


```

### checking IP address of container using shell of containers 

```
[root@docker-testing ~]# docker  exec ashuc1 ifconfig 
eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:02  
          inet addr:172.17.0.2  Bcast:172.17.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:12 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:1080 (1.0 KiB)  TX bytes:0 (0.0 B)


====
[root@docker-testing ~]# docker  exec ashuc2 ifconfig 
eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:03  
          inet addr:172.17.0.3  Bcast:172.17.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:8 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:640 (640.0 B)  TX bytes:0 (0.0 B)

```

### checking container networking details using json formatting 

```
[root@docker-testing ~]# docker inspect ashuc1 --format='{{.NetworkSettings.IPAddress}}'
172.17.0.2
[root@docker-testing ~]# docker inspect ashuc2 --format='{{.NetworkSettings.IPAddress}}'
172.17.0.3
[root@doc
```

### lets check their connections by sending icmp requests

```
[root@docker-testing ~]# docker ps
CONTAINER ID   IMAGE     COMMAND     CREATED         STATUS         PORTS     NAMES
237bddde318d   alpine    "/bin/sh"   2 minutes ago   Up 2 minutes             ashuc2
59f1c0fb3017   alpine    "/bin/sh"   2 minutes ago   Up 2 minutes             ashuc1
[root@docker-testing ~]# 
[root@docker-testing ~]# 
[root@docker-testing ~]# docker inspect ashuc2 --format='{{.NetworkSettings.IPAddress}}'
172.17.0.3
[root@docker-testing ~]# 
[root@docker-testing ~]# 
[root@docker-testing ~]# docker  exec -it ashuc1  sh 
/ # ping  172.17.0.3
PING 172.17.0.3 (172.17.0.3): 56 data bytes
64 bytes from 172.17.0.3: seq=0 ttl=64 time=0.090 ms
64 bytes from 172.17.0.3: seq=1 ttl=64 time=0.084 ms
^C
--- 172.17.0.3 ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 0.084/0.087/0.090 ms
/ # exit
[root@docker-testing
```

###  Checking NAT details by accessing Internet / any outside host location 

```
[root@docker-testing ~]# docker  exec -it ashuc1  sh 
/ # ping google.com 
PING google.com (142.251.16.113): 56 data bytes
64 bytes from 142.251.16.113: seq=0 ttl=95 time=2.173 ms
64 bytes from 142.251.16.113: seq=1 ttl=95 time=2.227 ms
^C
--- google.com ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 2.173/2.200/2.227 ms
/ # exit
```

