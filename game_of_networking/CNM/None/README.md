## Implementing None Networking using Docker cli 

### Listing all the defaults networks in Docker 

```
[root@docker-testing ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
d31fc38a486d   bridge    bridge    local
54e11a648da2   host      host      local
c9a5f9b78e3c   none      null      local
[root@docker-testing ~]# 

```

### Inspecting None Network 

```
[root@docker-testing ~]# docker network inspect none 
[
    {
        "Name": "none",
        "Id": "c9a5f9b78e3c5ba3e8d44c927c536395189118517f30806921808a8903e6464e",
        "Created": "2023-02-06T07:16:24.502649543Z",
        "Scope": "local",
        "Driver": "null",
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

### Lets create Containers in None Network 

```
[root@docker-testing ~]# docker  run -itd --name  testc1  --network none alpine 
Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
8921db27df28: Pull complete 
Digest: sha256:f271e74b17ced29b915d351685fd4644785c6d1559dd1f2d4189a5e851ef753a
Status: Downloaded newer image for alpine:latest
cf5d176edf0123cb0a4ca59861c7a5b032de2d31e4542d721ed961a3255b037d
[root@docker-testing ~]# 
[root@docker-testing ~]# docker  ps
CONTAINER ID   IMAGE     COMMAND     CREATED         STATUS         PORTS     NAMES
cf5d176edf01   alpine    "/bin/sh"   5 seconds ago   Up 4 seconds             testc1
[root@docker-testing ~]# 


```

### Note: You can use  '--net none' also in above command 

### Verify None Network 

```
[root@docker-testing ~]# docker network inspect none 
[
    {
        "Name": "none",
        "Id": "c9a5f9b78e3c5ba3e8d44c927c536395189118517f30806921808a8903e6464e",
        "Created": "2023-02-06T07:16:24.502649543Z",
        "Scope": "local",
        "Driver": "null",
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
            "1c6fd27ecd770859070659447231c8cbd8ef27fbbb23385f20d6f5242a6e94f8": {
                "Name": "testc1",
                "EndpointID": "9f425d5dc513e9a3490f9b4dcdd8ce74ef7904a88311d9e809c20a291587ccc5",
                "MacAddress": "",
                "IPv4Address": "",
                "IPv6Address": ""
            }

```

### verify container networking 

```
[root@docker-testing ~]# docker ps
CONTAINER ID   IMAGE     COMMAND     CREATED         STATUS         PORTS     NAMES
1c6fd27ecd77   alpine    "/bin/sh"   2 minutes ago   Up 2 minutes             testc1
[root@docker-testing ~]# 
[root@docker-testing ~]# 
[[root@docker-testing ~]# docker  exec -it testc1 sh 
/ # 
/ # ifconfig 
lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

/ # ping google.com 
ping: bad address 'google.com'
/ # ping 172.17.0.1 
PING 172.17.0.1 (172.17.0.1): 56 data bytes
ping: sendto: Network unreachable
/ # exit
[root@docker-testing ~]#
```

### Note:- For Instructor 

<ol>
    <li> make sure if you are using a different docker image then sh shell may not work os use bash also </li>
    <li> it is also possible that ifconfig command may not present in your container either install it of use some different command like ip addrs </li>
    <li> it is also possible that ping command is also not there so install it as per your container image </li>
</ol>


## Using None Networking with docker compose 

### Verify docker compose is installed

```
[root@docker-testing ~]# docker-compose version 
Docker Compose version v2.15.1
[root@docker-testing ~]# 
[root@docker-testing ~]# docker-compose -v
Docker Compose version v2.15.1
[root@docker-testing ~]# 
```

### creating docker-compose file 

```

```
