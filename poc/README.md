# Poc 
## Docker and iptables: a dynamic duo for networking virtuosity

### More understanding about iptables with docker 



<ol>
    <li> In Chain FORWARD, the first rule states that all traffic from any source and destination is sent to the DOCKER-USER chain. </li>
    <li> In the DOCKER-USER chain, there is only one rule that all traffic from any source to any destination has a target RETURN, returning to the FORWARD chain.</li>
    <li> Then, the second rule in Chain FORWARD states that all traffic from any source and destination is sent to the DOCKER-ISOLATION-STAGE-1 chain. </li>
    <li> The DOCKER-ISOLATION-STAGE-1 chain has two rules </li>
    <li> Rule 1: All traffic from the docker0 interface to any interface except itself (!docker0) is sent to the DOCKER-ISOLATION-STAGE-2 chain.</li>
    <li> Rule 2: All other traffic is returned to its originating chain (e.g., FORWARD or INPUT). </li>
    <li> The DOCKER-ISOLATION-STAGE-2 chain also has two rules. </li>
    <li> Rule 1: All traffic from any interface to the docker0 interface is dropped. </li>
    <li> Rule 2: All other traffic is returned to its originating chain. </li>
</ol>
