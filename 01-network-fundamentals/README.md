# Network Fundamentals — Basic LAN Configuration

## Objective

Configure two computers on the same IPv4 network using a Cisco switch and verify connectivity between the hosts.

## Lab Environment

- Cisco Packet Tracer
- 2 × PCs
- 1 × Cisco 2960 switch
- IPv4

## Network Topology

![Network Topology](topology.png)

## IP Configuration

| Device | IP Address | Subnet Mask |
|---|---|---|
| PC0 | 192.168.1.10 | 255.255.255.0 |
| PC1 | 192.168.1.20 | 255.255.255.0 |

Both hosts belong to the `192.168.1.0/24` network.

## Connectivity Test

From PC0, I tested connectivity to PC1 using:

```text
ping 192.168.1.20
