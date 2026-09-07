# Project 03 - DHCP

## Overview

In this project, I designed and implemented a small organizational network using **Cisco Packet Tracer**.

The main purpose of this project was to gain practical experience with **DHCP, IPv4 addressing, subnetting, default gateways, DNS, routing, connectivity testing, and network troubleshooting**.

I configured a **Cisco 2911 router as a DHCP server** and created two separate networks for two different departments:

- Accounting
- IT

The router provides IP configuration to the client PCs automatically using DHCP, so I did not need to manually configure IP addresses on each PC.

---

## Network Devices

I used the following devices in this project:

| Device | Model      | Name      |
| ------ | ---------- | --------- |
| Router | Cisco 2911 | `R1-DHCP` |
| Switch | Cisco 2960 | `SW-ACC`  |
| Switch | Cisco 2960 | `SW-IT`   |
| PC     | Generic PC | `ACC-PC1` |
| PC     | Generic PC | `ACC-PC2` |
| PC     | Generic PC | `ACC-PC3` |
| PC     | Generic PC | `IT-PC1`  |
| PC     | Generic PC | `IT-PC2`  |
| PC     | Generic PC | `IT-PC3`  |

---

# Network Design

I divided the network into two different IPv4 networks.

### Accounting Network

```text
Network:        192.168.10.0/24
Subnet Mask:    255.255.255.0
Default Gateway: 192.168.10.1
```

### IT Network

```text
Network:        192.168.20.0/24
Subnet Mask:    255.255.255.0
Default Gateway: 192.168.20.1
```

The router interfaces were configured as follows:

| Router Interface | IP Address        | Network    |
| ---------------- | ----------------- | ---------- |
| `G0/0`           | `192.168.10.1/24` | Accounting |
| `G0/1`           | `192.168.20.1/24` | IT         |

---

# Network Topology

The basic topology of the project is:

```text
                    R1-DHCP
                  Cisco 2911
                 /          \
                /            \
        G0/0                  G0/1
   192.168.10.1          192.168.20.1
            |                  |
            |                  |
         SW-ACC              SW-IT
       Cisco 2960          Cisco 2960
        /  |  \              / | \
       /   |   \            /  |  \
    PC1   PC2   PC3       PC1  PC2  PC3
```

The PCs in the Accounting department belong to the `192.168.10.0/24` network, while the PCs in the IT department belong to the `192.168.20.0/24` network.

---

# Router Configuration

First, I accessed the router CLI and entered privileged EXEC mode:

```bash
enable
```

Then I entered global configuration mode:

```bash
configure terminal
```

I changed the router hostname to make it easier to identify:

```bash
hostname R1-DHCP
```

The prompt then changed to:

```text
R1-DHCP(config)#
```

---

# Configuring GigabitEthernet0/0

I configured `GigabitEthernet0/0` for the Accounting network:

```bash
interface gigabitEthernet 0/0
ip address 192.168.10.1 255.255.255.0
no shutdown
```

The `no shutdown` command enables the interface.

After enabling the interface, Packet Tracer displayed a message similar to:

```text
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up
```

This indicates that the interface became active.

---

# Configuring GigabitEthernet0/1

I then configured the second router interface for the IT network:

```bash
interface gigabitEthernet 0/1
ip address 192.168.20.1 255.255.255.0
no shutdown
```

Again, the interface changed to the `up` state.

---

# Verifying Router Interfaces

To verify the IP addresses and interface status, I used:

```bash
show ip interface brief
```

The output was similar to:

```text
Interface              IP-Address      OK? Method Status       Protocol
GigabitEthernet0/0     192.168.10.1    YES manual up           up
GigabitEthernet0/1     192.168.20.1    YES manual up           up
```

This command is useful because it allows me to quickly check:

- Interface name
- IP address
- Interface status
- Protocol status

Both interfaces needed to be `up/up`.

---

# DHCP Configuration

After configuring the router interfaces, I configured the router to work as a DHCP server.

Before creating the DHCP pools, I excluded some IP addresses from DHCP.

For the Accounting network:

```bash
ip dhcp excluded-address 192.168.10.1 192.168.10.20
```

For the IT network:

```bash
ip dhcp excluded-address 192.168.20.1 192.168.20.20
```

This means that these addresses will not be assigned to DHCP clients.

For example, the router's IP address:

```text
192.168.10.1
```

must not be assigned to another device because it is already being used by the router.

The same applies to:

```text
192.168.20.1
```

---

# Creating the Accounting DHCP Pool

I created a DHCP pool named `ACCOUNTING`:

```bash
ip dhcp pool ACCOUNTING
```

Then I specified the network:

```bash
network 192.168.10.0 255.255.255.0
```

I configured the default gateway:

```bash
default-router 192.168.10.1
```

I also configured the DNS server:

```bash
dns-server 8.8.8.8
```

Finally, I configured the domain name:

```bash
domain-name suhatech.local
```

The complete configuration was:

```bash
ip dhcp pool ACCOUNTING
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8
domain-name suhatech.local
exit
```

---

# Creating the IT DHCP Pool

I created another DHCP pool for the IT department:

```bash
ip dhcp pool IT
```

Then I configured its network:

```bash
network 192.168.20.0 255.255.255.0
```

Default gateway:

```bash
default-router 192.168.20.1
```

DNS server:

```bash
dns-server 8.8.8.8
```

Domain name:

```bash
domain-name suhatech.local
```

The complete configuration was:

```bash
ip dhcp pool IT
network 192.168.20.0 255.255.255.0
default-router 192.168.20.1
dns-server 8.8.8.8
domain-name suhatech.local
exit
```

---

# Checking DHCP Pools

After configuring DHCP, I used the following command to verify the DHCP pools:

```bash
show ip dhcp pool
```

The output contained information similar to:

```text
Pool ACCOUNTING :
 Total addresses                : 254
 Leased addresses               : 3

Pool IT :
 Total addresses                : 254
 Leased addresses               : 3
```

The exact output can vary depending on how many clients have already requested an IP address.

This command helped me verify that the DHCP pools were created successfully.

---

# Checking DHCP Bindings

To see which IP addresses had been assigned to clients, I used:

```bash
show ip dhcp binding
```

The output was similar to:

```text
Bindings from all pools not associated with VRF:

IP address       Client-ID/              Lease expiration
                 Hardware address
192.168.10.21    ...                     ...
192.168.10.22    ...                     ...
192.168.10.23    ...                     ...
192.168.20.21    ...                     ...
192.168.20.22    ...                     ...
192.168.20.23    ...                     ...
```

This confirmed that the router had successfully assigned IP addresses to the PCs.

The exact IP addresses may be different depending on the order in which the PCs request DHCP addresses.

---

# Configuring PCs for DHCP

On each PC, I went to:

```text
Desktop
→ IP Configuration
→ DHCP
```

I repeated this process for all six PCs.

The Accounting PCs received addresses from:

```text
192.168.10.0/24
```

while the IT PCs received addresses from:

```text
192.168.20.0/24
```

The clients also automatically received:

```text
Subnet Mask
Default Gateway
DNS Server
```

from the DHCP server.

---

# Testing IP Configuration

On the PCs, I opened:

```text
Desktop
→ Command Prompt
```

and used:

```bash
ipconfig
```

A typical result was similar to:

```text
IP Address................: 192.168.10.21
Subnet Mask...............: 255.255.255.0
Default Gateway...........: 192.168.10.1
DNS Server................: 8.8.8.8
```

For an IT PC, the result was similar to:

```text
IP Address................: 192.168.20.21
Subnet Mask...............: 255.255.255.0
Default Gateway...........: 192.168.20.1
DNS Server................: 8.8.8.8
```

---

# Testing Connectivity with Ping

I used the `ping` command to test network connectivity.

For example, from an Accounting PC:

```bash
ping 192.168.10.1
```

A successful result was similar to:

```text
Reply from 192.168.10.1: bytes=32 time<1ms TTL=255
Reply from 192.168.10.1: bytes=32 time<1ms TTL=255
Reply from 192.168.10.1: bytes=32 time<1ms TTL=255
Reply from 192.168.10.1: bytes=32 time<1ms TTL=255
```

This confirmed connectivity between the PC and its default gateway.

---

# Testing Communication Between Networks

I also tested communication between the Accounting and IT networks.

For example, from an Accounting PC:

```bash
ping 192.168.20.1
```

If the ping was successful, it confirmed that the Accounting PC could reach the router interface connected to the IT network.

I also tested PC-to-PC communication between the two networks, for example:

```bash
ping 192.168.20.21
```

This verified that the router could forward traffic between:

```text
192.168.10.0/24
```

and:

```text
192.168.20.0/24
```

---

# Checking the Routing Table

To verify the routes installed on the router, I used:

```bash
show ip route
```

The output contained entries similar to:

```text
C    192.168.10.0/24 is directly connected, GigabitEthernet0/0
C    192.168.20.0/24 is directly connected, GigabitEthernet0/1
```

The letter `C` means:

```text
Connected
```

This means that both networks are directly connected to the router.

---

# Troubleshooting

I also practiced basic network troubleshooting.

If a PC did not receive an IP address, I checked its configuration using:

```bash
ipconfig
```

Then I checked the router interfaces using:

```bash
show ip interface brief
```

I checked the DHCP pools using:

```bash
show ip dhcp pool
```

I checked the assigned DHCP addresses using:

```bash
show ip dhcp binding
```

I also checked the routing table using:

```bash
show ip route
```

Finally, I used `ping` to identify connectivity problems.

---

# Saving the Configuration

After completing the configuration, I saved the running configuration to NVRAM using:

```bash
copy running-config startup-config
```

The router asked:

```text
Destination filename [startup-config]?
```

I pressed **Enter** to accept the default filename.

This ensures that the configuration is saved and can be loaded again after a reboot.

# Final IP Addressing Plan

| Device    | Interface | IP Address        | Configuration |
| --------- | --------- | ----------------- | ------------- |
| `R1-DHCP` | G0/0      | `192.168.10.1/24` | Static        |
| `R1-DHCP` | G0/1      | `192.168.20.1/24` | Static        |
| `ACC-PC1` | Fa0       | `192.168.10.x`    | DHCP          |
| `ACC-PC2` | Fa0       | `192.168.10.x`    | DHCP          |
| `ACC-PC3` | Fa0       | `192.168.10.x`    | DHCP          |
| `IT-PC1`  | Fa0       | `192.168.20.x`    | DHCP          |
| `IT-PC2`  | Fa0       | `192.168.20.x`    | DHCP          |
| `IT-PC3`  | Fa0       | `192.168.20.x`    | DHCP          |
