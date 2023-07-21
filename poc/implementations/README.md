# Poc implementing 

## Iptables behind Docker 

### Docker and Docker-user CHAIN checking 

```bash
[root@ip-172-31-5-47 test]# iptables -nvL 
Chain INPUT (policy ACCEPT 1096K packets, 304M bytes)
 pkts bytes target     prot opt in     out     source               destination         


Chain FORWARD (policy DROP 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 DOCKER-USER  all  --  *      *       0.0.0.0/0            0.0.0.0/0           
    0     0 DOCKER-ISOLATION-STAGE-1  all  --  *      *       0.0.0.0/0            0.0.0.0/0           
    0     0 ACCEPT     all  --  *      docker0  0.0.0.0/0            0.0.0.0/0            ctstate RELATED,ESTABLISHED
    0     0 DOCKER     all  --  *      docker0  0.0.0.0/0            0.0.0.0/0           
    0     0 ACCEPT     all  --  docker0 !docker0  0.0.0.0/0            0.0.0.0/0           
    0     0 ACCEPT     all  --  docker0 docker0  0.0.0.0/0            0.0.0.0/0           



Chain OUTPUT (policy ACCEPT 1090K packets, 159M bytes)
 pkts bytes target     prot opt in     out     source               destination         


Chain DOCKER (1 references)
 pkts bytes target     prot opt in     out     source               destination         


Chain DOCKER-ISOLATION-STAGE-1 (1 references)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 DOCKER-ISOLATION-STAGE-2  all  --  docker0 !docker0  0.0.0.0/0            0.0.0.0/0           
    0     0 RETURN     all  --  *      *       0.0.0.0/0            0.0.0.0/0           


Chain DOCKER-ISOLATION-STAGE-2 (1 references)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 DROP       all  --  *      docker0  0.0.0.0/0            0.0.0.0/0           
    0     0 RETURN     all  --  *      *       0.0.0.0/0            0.0.0.0/0           


Chain DOCKER-USER (1 references)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 RETURN     all  --  *      *       0.0.0.0/0            0.0.0.0/0           
[root@ip-172-31-5-47 test]# 



```
###  Most important Note 

<details>
 <summary>click_here</summary>
   <b> DOCKER </b> chain is auto managed by Docker daemon so never edit it manually 
    <br>
    <b> DOCKER-USER </b> Only chain where you can edit iptables rules manually  
</details>

### There is NAT table entry also for docker iptables rule 

```bash
[root@ip-172-31-5-47 ~]# iptables -t nat -nvL 
Chain PREROUTING (policy ACCEPT 4297 packets, 200K bytes)
 pkts bytes target     prot opt in     out     source               destination         
 4297  200K DOCKER     all  --  *      *       0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type LOCAL

Chain INPUT (policy ACCEPT 4297 packets, 200K bytes)
 pkts bytes target     prot opt in     out     source               destination         

Chain OUTPUT (policy ACCEPT 2482 packets, 181K bytes)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 DOCKER     all  --  *      *       0.0.0.0/0           !127.0.0.0/8          ADDRTYPE match dst-type LOCAL

Chain POSTROUTING (policy ACCEPT 2482 packets, 181K bytes)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 MASQUERADE  all  --  *      !docker0  172.17.0.0/16        0.0.0.0/0           

Chain DOCKER (2 references)
 pkts bytes target     prot opt in     out     source               destination         
    0     0 RETURN     all  --  docker0 *       0.0.0.0/0            0.0.0.0/0           
[root@ip-172-31-5-47 ~]# 


```

## Secret 🅰️

<details>
 <summary>Touch Me</summary>
  
    <b> DOCKER-USER </b> can be used to block particular containers in the same bridge from access each other 

</details>

### iptables new rule 

```
  [root@ip-172-31-5-47 ~]# iptables  -I DOCKER-USER -s 172.17.0.2 -d 172.17.0.3 -j DROP  
        [root@ip-172-31-5-47 ~]# docker exec -it c1 sh 
        / # ping 172.17.0.3
        PING 172.17.0.3 (172.17.0.3): 56 data bytes
        ^C
        --- 172.17.0.3 ping statistics ---
        2 packets transmitted, 0 packets received, 100% packet loss
        / # 
```

#  Restricting Docker host ◀️

## Restricting docker host 

### Restricting 0.0.0.0 in public port 

***
By default, the Docker daemon binds published container ports to the 0.0.0.0 address.
***
