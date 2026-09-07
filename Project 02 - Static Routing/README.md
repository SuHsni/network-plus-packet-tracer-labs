# Project 02 — Basic Inter-LAN Routing

## Overview

In this project, I designed and configured a small network using **Cisco Packet Tracer**.

The goal was to connect two separate LANs through a router, configure IP addressing and default gateways, and verify connectivity between the networks.

This project is designed at a **Network+ level**.

---

## Topology

**Devices:**

- 1 × Cisco 1841 Router
- 2 × Cisco 2960-24TT Switches
- 2 × PCs
- Copper Straight-Through cables

**Device Names:**

| Device   | Name           |
| -------- | -------------- |
| Router   | `RTR-EDGE-01`  |
| Switch 1 | `SW-LAN-01`    |
| Switch 2 | `SW-LAN-02`    |
| PC 1     | `PC-CLIENT-01` |
| PC 2     | `PC-CLIENT-02` |

```text
PC-CLIENT-01
     |
SW-LAN-01
     |
Fa0/0
     |
RTR-EDGE-01
     |
Fa0/1
     |
SW-LAN-02
     |
PC-CLIENT-02
```

---

## IP Addressing

### LAN 1

```text
Network: 192.168.10.0/24
Gateway: 192.168.10.1
```

**Router — Fa0/0**

```text
IP Address: 192.168.10.1
Subnet Mask: 255.255.255.0
```

**PC-CLIENT-01**

```text
IP Address: 192.168.10.10
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.10.1
```

### LAN 2

```text
Network: 192.168.20.0/24
Gateway: 192.168.20.1
```

**Router — Fa0/1**

```text
IP Address: 192.168.20.1
Subnet Mask: 255.255.255.0
```

**PC-CLIENT-02**

```text
IP Address: 192.168.20.10
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.20.1
```

---

## Router Configuration

First, I configured the router hostname:

```text
enable
configure terminal
hostname RTR-EDGE-01
```

Then I configured both router interfaces:

```text
interface fastethernet 0/0
ip address 192.168.10.1 255.255.255.0
no shutdown
exit

interface fastethernet 0/1
ip address 192.168.20.1 255.255.255.0
no shutdown
exit

end
```

---

## Verification

I checked the interface status with:

```text
show ip interface brief
```

Both interfaces should show:

```text
up / up
```

I also checked the routing table:

```text
show ip route
```

Because both networks are directly connected to the router, the router automatically creates **Connected (`C`) routes**:

```text
C 192.168.10.0/24 is directly connected, FastEthernet0/0
C 192.168.20.0/24 is directly connected, FastEthernet0/1
```

No static routes are required in this topology.

---

## Connectivity Testing

From `PC-CLIENT-01`, I tested:

```text
ping 192.168.10.1
ping 192.168.20.1
ping 192.168.20.10
```

All tests were successful.

This confirmed that the two LANs can communicate through the router.

```text
PC-CLIENT-01
192.168.10.10
      |
      v
SW-LAN-01
      |
      v
RTR-EDGE-01
      |
      v
SW-LAN-02
      |
      v
PC-CLIENT-02
192.168.20.10
```

---

## Switch Configuration

No IP configuration was required on the switches for this basic Layer 2 topology.

The switches provide Layer 2 connectivity between the PCs and the router.

---

## What I Learned

Through this project, I practiced:

- Building a basic network topology in Cisco Packet Tracer
- Configuring router interfaces
- Assigning IP addresses and subnet masks
- Configuring default gateways
- Understanding directly connected routes
- Using `no shutdown`
- Verifying interfaces with `show ip interface brief`
- Checking the routing table with `show ip route`
- Testing connectivity with `ping`
- Understanding basic inter-LAN routing
