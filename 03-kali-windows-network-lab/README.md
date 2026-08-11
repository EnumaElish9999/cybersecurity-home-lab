# Lab 3 — Kali Linux & Windows Virtual Network

## Objective

Build an isolated virtual network between Kali Linux and Windows 10 using VirtualBox and prepare the environment for network traffic analysis with Wireshark.

## Environment

- VirtualBox
- Kali Linux 2025.2
- Windows 10
- Wireshark

## Network Topology

![Network Topology](screenshots/network-topology.png)

## Virtual Network Configuration

### Kali Linux

| Interface | Network | IP Address |
|---|---|---|
| eth0 | NAT | 10.0.2.15/24 |
| eth1 | CyberLab | 10.10.10.1/24 |

### Windows 10

| Interface | Network | IP Address |
|---|---|---|
| Ethernet | CyberLab | 10.10.10.10/24 |

## Configuration

Kali's internal interface was configured with:

```text
10.10.10.1/24
