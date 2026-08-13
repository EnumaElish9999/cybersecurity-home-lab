#Lab 4 - DNS Investigation

## Objective
Using the home lab from Lab 3, capture and analyse live DNS traffic, identifying who made each request, where it went, what domain was queried, and what IP was returned, while demonstrating how DNS data aids in security investigations like malware detection and data exfiltration.

## Environment

- VirtualBox
- Kali Linux 2025.2
- Windows 10
- Wireshark

## Network Topology

```text
             Internet
                │
               NAT
                │
        ┌───────┴───────┐
        │     Kali      │
        │               │
        │ eth0          │
        │ 10.0.2.15/24  │
        │               │
        │ eth1          │
        │ 10.10.10.1/24 │
        └───────┬───────┘
                │
          CyberLab LAN
          10.10.10.0/24
                │
        ┌───────┴───────┐
        │   Windows 10  │
        │ 10.10.10.10/24│
        └───────────────┘
```

