#
```mermaid
sequenceDiagram
    autonumber
    participant C as Client
    participant R as Router (MTU = 1000)
    participant S as Server

    Note over C,R,S: Scenario 1 – ICMP Ping with DF (-M do)

    C->>R: ICMP Echo (DF=1, payload=972)
    R->>S: Forward ICMP (fits MTU)
    S->>R: ICMP Echo Reply
    R->>C: Forward Reply
    Note over C: Ping succeeds (MTU OK)

    C->>R: ICMP Echo (DF=1, payload=1200)
    R-->>C: ICMP Unreachable<br/>Fragmentation Needed (MTU=1000)
    Note over C: Ping fails, PMTU learned

    Note over C,R,S: Scenario 3 – TCP PMTUD using hping3

    C->>R: TCP SYN (DF=1, payload=1200)
    R-->>C: ICMP Unreachable<br/>Fragmentation Needed (MTU=1000)
    Note over C: TCP stack adjusts MSS / PMTU

    C->>R: TCP SYN (DF=1, payload=972)
    R->>S: Forward TCP SYN
    S->>R: TCP SYN-ACK
    R->>C: Forward SYN-ACK
    Note over C,S: TCP connection can proceed
```
