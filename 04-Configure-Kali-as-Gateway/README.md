# Lab 3 - Configure Kali as a Gateway

## Objective 

Configure Kali Linux to act as a Gateway for Windows so that Kali can forward traffic from Windows to Kali's NAT interface.

## Environment

- VirtualBox
- Kali Linux 2025.2
- Windows 10
- Wireshark

## Network topology

```text
        Windows
      10.10.10.10
           │
           │ CyberLab
           ↓
        Kali eth1
      10.10.10.1
           │
           │ Kali routes/NATs traffic
           ↓
        Kali eth0
      10.0.2.15
           │
           ↓
      VirtualBox NAT
           │
           ↓
        Internet
```

## Step 1 - Enable IP Forwarding

Kali was configured to forward IPv4 traffic between its network interfaces.

Command used:

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```
Result: 1

This allows Kali to route IPv4 traffic between the CyberLab interface (eth1) and the NAT interface (eth0).

## Step 2 - Initial Connectivity Test

Testing routing using Windows.

```bash
ping 8.8.8.8
```
![PingTest](pingtest.png)

The ping test timed out and failed.

NAT was not configured; Windows could reach Kali but could not reach the Internet.

- Windows → Kali      ✅
- Windows → 8.8.8.8   ❌

This demonstrated that IP forwarding alone was not sufficient for Windows to access the Internet through Kali.

## Step 3 - Configure NAT on Kali

Kali translates Windows' private 10.10.10.0/24 traffic into Kali's Internet-facing address on eth0.

### NAT rule 

```bash
sudo iptables -t nat -A POSTROUTING -s 10.10.10.0/24 -o eth0 -j MASQUERADE
```
Traffic from the Windows network leaves Kali through eth0; translate its address to access the Internet.
The MASQUERADE rule allows Windows to access the Internet using Kali's network connection.

### Allow forwarding

```bash
sudo iptables -A FORWARD -i eth1 -o eth0 -s 10.10.10.0/24 -j ACCEPT
```

```bash
sudo iptables -A FORWARD -i eth0 -o eth1 -d 10.10.10.0/24 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
```
The first rule allows:

Windows → Kali eth1 → Kali eth0 → Internet

The second rule allows the replies to return:

Internet → Kali eth0 → Kali eth1 → Windows


## Step 4 - Test Internet Connectivity

After configuring NAT and forwarding, Windows was tested again.

```bash
ping 8.8.8.8
```
![PingTestAgain](testagain.png)

## Result

The test was successful.

Windows → Kali             ✅
Windows → 8.8.8.8          ✅
Packet loss:               0%

This confirmed that Kali was successfully routing and NATing traffic from the Windows VM to the Internet.


