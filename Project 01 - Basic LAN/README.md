# Project 01 — Basic LAN

## Overview

In this project, I built a simple Local Area Network using Cisco Packet Tracer.

My goal was to practice the basic concepts of networking that I have learned at the Network+ level, including Ethernet connections, IPv4 addressing, subnet masks, switches, and connectivity testing with `ping`.

---

## Topology

For this lab, I used:

- 2 PCs
- 1 Cisco 2960 Switch

I connected both PCs directly to the switch using **Copper Straight-Through** cables.

```text
PC-CLIENT-01
     |
     |
SW-LAN-01
     |
     |
PC-CLIENT-02
```

### Device Names

I renamed the devices to make the topology easier to understand:

| Device       | Role           |
| ------------ | -------------- |
| PC-CLIENT-01 | Client         |
| PC-CLIENT-02 | Client         |
| SW-LAN-01    | Layer 2 Switch |

---

## IP Addressing

I configured both PCs manually with IPv4 addresses.

I used the `192.168.10.0/24` network.

| Device       | IPv4 Address    | Subnet Mask     |
| ------------ | --------------- | --------------- |
| PC-CLIENT-01 | `192.168.10.10` | `255.255.255.0` |
| PC-CLIENT-02 | `192.168.10.20` | `255.255.255.0` |

I did not configure a Default Gateway because both devices are in the same subnet and there is no router in this topology.

---

## Connectivity Test

After configuring the IP addresses, I tested the connection between the two PCs.

From `PC-CLIENT-01`, I ran:

```text
ping 192.168.10.20
```

The ping was successful and I received replies from `PC-CLIENT-02`.

I also tested the connection in the opposite direction from `PC-CLIENT-02`:

```text
ping 192.168.10.10
```

This test was also successful.

---

## Checking the Configuration

I used the following command on both PCs:

```text
ipconfig
```

This allowed me to verify the IPv4 address and subnet mask configured on each machine.

The configuration was:

```text
PC-CLIENT-01
IPv4 Address: 192.168.10.10
Subnet Mask: 255.255.255.0
```

```text
PC-CLIENT-02
IPv4 Address: 192.168.10.20
Subnet Mask: 255.255.255.0
```

---

## Troubleshooting Test

After confirming that the original configuration was working, I intentionally changed the IP address of `PC-CLIENT-02`.

I changed it from:

```text
192.168.10.20
```

to:

```text
192.168.20.20
```

I kept the subnet mask as:

```text
255.255.255.0
```

I then tried to ping the new address from `PC-CLIENT-01`:

```text
ping 192.168.20.20
```

The communication failed.

### Why did it fail?

The two PCs were now in different networks:

```text
PC-CLIENT-01
192.168.10.10/24
        |
        |
    SW-LAN-01
        |
        |
PC-CLIENT-02
192.168.20.20/24
```

Since I only had a Layer 2 switch and no router, there was no device available to route traffic between the two different networks.

I then changed `PC-CLIENT-02` back to:

```text
192.168.10.20
```

After restoring the original configuration, I tested the connection again and the ping worked successfully.

---

## What I Learned

Through this lab, I practiced:

- Building a basic LAN
- Connecting end devices to a switch
- Using Ethernet straight-through cables
- Configuring IPv4 addresses manually
- Understanding subnet masks
- Understanding same-subnet communication
- Using `ipconfig` to verify network configuration
- Using `ping` to test connectivity
- Identifying a basic subnet-related connectivity problem
- Understanding why a router is required to communicate between different IP networks
