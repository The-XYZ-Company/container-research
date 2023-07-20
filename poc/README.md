# Poc 
## Docker and iptables: a dynamic duo for networking virtuosity

### More understanding 

<p> 
In Chain FORWARD, the first rule states that all traffic from any source and destination is sent to the DOCKER-USER chain.
In the DOCKER-USER chain, there is only one rule that all traffic from any source to any destination has a target RETURN, returning to the FORWARD chain.
Then, the second rule in Chain FORWARD states that all traffic from any source and destination is sent to the DOCKER-ISOLATION-STAGE-1 chain.
The DOCKER-ISOLATION-STAGE-1 chain has two rules.
Rule 1: All traffic from the docker0 interface to any interface except itself (!docker0) is sent to the DOCKER-ISOLATION-STAGE-2 chain.
Rule 2: All other traffic is returned to its originating chain (e.g., FORWARD or INPUT).
The DOCKER-ISOLATION-STAGE-2 chain also has two rules.
Rule 1: All traffic from any interface to the docker0 interface is dropped.
Rule 2: All other traffic is returned to its originating chain.
</p>
