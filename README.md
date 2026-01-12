# Scenario 1: MTU on ingress eth1
```mermaid
graph LR
    C["Client<br/>eth0<br/>MTU 1500<br/>"]
    R["Router<br/>eth0 MTU 1500<br/>eth1 MTU 1000 IP"]
    S["Server<br/>eth0<br/>MTU 1500<br/>"]

    C --|eth0 → eth1|--> R
    R --|eth0 → eth0|--> S
```
## Testing
```
pawel@worker-1:~$ ping -M do -s 1472 10.171.176.130
PING 10.171.176.130 (10.171.176.130) 1472(1500) bytes of data.
1480 bytes from 10.171.176.130: icmp_seq=1 ttl=63 time=0.675 ms
1480 bytes from 10.171.176.130: icmp_seq=2 ttl=63 time=0.603 ms
```
As we can see, the packet with payload of 1472 (+32 ICMP and IP overhead) is still allowed through despite MTU set to 1000 on eth1 (ingress of the Router)

On router, we can observe that we will accept packets with size of 1472 but the response is with length of MTU of eth1 (1000-24=976):
```
root@router-1:~# tcpdump -iany -n -nn icmp and host 10.1.1.20
tcpdump: data link type LINUX_SLL2
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on any, link-type LINUX_SLL2 (Linux cooked v2), snapshot length 262144 bytes
09:51:20.514954 ens33 In  IP 10.1.1.20 > 10.171.176.130: ICMP echo request, id 58085, seq 18, length 1480
09:51:20.514973 ens32 Out IP 10.1.1.20 > 10.171.176.130: ICMP echo request, id 58085, seq 18, length 1480
09:51:20.515464 ens32 In  IP 10.171.176.130 > 10.1.1.20: ICMP echo reply, id 58085, seq 18, length 976
09:51:20.515464 ens32 In  IP 10.171.176.130 > 10.1.1.20: ip-proto-1
09:51:20.515485 ens33 Out IP 10.171.176.130 > 10.1.1.20: ICMP echo reply, id 58085, seq 18, length 976
09:51:20.515493 ens33 Out IP 10.171.176.130 > 10.1.1.20: ip-proto-1
```
