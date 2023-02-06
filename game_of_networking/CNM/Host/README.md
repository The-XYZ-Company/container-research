## Exploring host Network Mode using docker cli

### Lets check default networks 

```
[root@docker-testing ~]# docker network ls 
NETWORK ID     NAME      DRIVER    SCOPE
d31fc38a486d   bridge    bridge    local
54e11a648da2   host      host      local
c9a5f9b78e3c   none      null      local
```

### Lets Inspect host bridge to see is there any containers connected 

```
[root@docker-testing ~]# docker network inspect host 
[
    {
        "Name": "host",
        "Id": "54e11a648da248108d108e380a057af8d5d7e5147b8eb310b0cf500551a78ea5",
        "Created": "2023-02-06T07:16:24.518570416Z",
        "Scope": "local",
        "Driver": "host",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": []
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]

```

### Now creating container using Host network 

```
[root@docker-testing ~]# docker  run -itd --name  xyzc1 --network host  alpine 
98e18c8e1f316b274e59568ea7da280e9069c87d740386f67a53da6e25566c96
```

### Verify host bridge again 

```
[root@docker-testing ~]# docker network inspect host 
[
    {
        "Name": "host",
        "Id": "54e11a648da248108d108e380a057af8d5d7e5147b8eb310b0cf500551a78ea5",
        "Created": "2023-02-06T07:16:24.518570416Z",
        "Scope": "local",
        "Driver": "host",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": []
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "98e18c8e1f316b274e59568ea7da280e9069c87d740386f67a53da6e25566c96": {
                "Name": "xyzc1",
                "EndpointID": "5eed3d03a13afdc28f4faa44e34ad89c5ad55285781f4a66e8050cc56ce9fc6e",
                "MacAddress": "",
                "IPv4Address": "",
                "IPv6Address": ""
            }

```

## before we verify container network 

### lets check host system network interfaces 

```
[root@docker-testing ~]# ifconfig 
docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::42:faff:fe54:93d0  prefixlen 64  scopeid 0x20<link>
        ether 02:42:fa:54:93:d0  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 4  bytes 440 (440.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 172.31.53.114  netmask 255.255.240.0  broadcast 172.31.63.255
        inet6 fe80::4d6:43ff:fe4e:60b  prefixlen 64  scopeid 0x20<link>
        ether 06:d6:43:4e:06:0b  txqueuelen 1000  (Ethernet)
        RX packets 159729  bytes 217387406 (207.3 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 22471  bytes 2289038 (2.1 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 48  bytes 3888 (3.7 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 48  bytes 3888 (3.7 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0


```

### Now checking container network interfaces 

```
[root@docker-testing ~]# docker  ps
CONTAINER ID   IMAGE     COMMAND     CREATED         STATUS         PORTS     NAMES
98e18c8e1f31   alpine    "/bin/sh"   3 minutes ago   Up 3 minutes             xyzc1
[root@docker-testing ~]# docker  exec -it xyzc1 sh 
/ # ifconfig 
docker0   Link encap:Ethernet  HWaddr 02:42:FA:54:93:D0  
          inet addr:172.17.0.1  Bcast:172.17.255.255  Mask:255.255.0.0
          inet6 addr: fe80::42:faff:fe54:93d0/64 Scope:Link
          UP BROADCAST MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:4 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:0 (0.0 B)  TX bytes:440 (440.0 B)

eth0      Link encap:Ethernet  HWaddr 06:D6:43:4E:06:0B  
          inet addr:172.31.53.114  Bcast:172.31.63.255  Mask:255.255.240.0
          inet6 addr: fe80::4d6:43ff:fe4e:60b/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:9001  Metric:1
          RX packets:160385 errors:0 dropped:0 overruns:0 frame:0
          TX packets:23028 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:217438589 (207.3 MiB)  TX bytes:2393973 (2.2 MiB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:48 errors:0 dropped:0 overruns:0 frame:0
          TX packets:48 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:3888 (3.7 KiB)  TX bytes:3888 (3.7 KiB)

/ # ping google.com 
PING google.com (142.251.16.100): 56 data bytes
64 bytes from 142.251.16.100: seq=0 ttl=49 time=2.119 ms
64 bytes from 142.251.16.100: seq=1 ttl=49 time=2.181 ms
^C
--- google.com ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 2.119/2.150/2.181 ms
/ # 
/ # exit
[root@docker-testing ~]# 
[root@docker-testing ~]#
```

### Note:- For Instructor 

<ol>
    <li> make sure if you are using a different docker image then sh shell may not work os use bash also </li>
    <li> it is also possible that ifconfig command may not present in your container either install it of use some different command like ip addrs </li>
    <li> it is also possible that ping command is also not there so install it as per your container image </li>
</ol>

### enjoy Learning ..
