#
```mermaid
graph LR
    C["Client<br/>eth0<br/>MTU 1400<br/>"]
    R["Router<br/>eth0 MTU 1400<br/>eth1 MTU 1000 IP"]
    S["Server<br/>eth0<br/>MTU 1400<br/>"]

    C --|eth0 → eth1|--> R
    R --|eth0 → eth0|--> S


```
