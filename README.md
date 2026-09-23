# Enterprise LAN Design with VLAN Segmentation, Subnetting & DHCP Automation

## Overview
This project simulates a single-site enterprise LAN for a fictional company with three departments — Sales, HR, and IT. It uses VLSM-based subnetting to efficiently allocate IP address space, VLANs to logically separate department traffic, inter-VLAN routing to allow controlled communication between departments, and DHCP to automatically assign IP addresses to end devices. Built and tested in Cisco Packet Tracer.

## Tools Used
- Cisco Packet Tracer (v8.x)
- Cisco IOS (Router: 2911 series, Layer 3 Switch: 3560 series, Access Switches: 2960 series)

## Network Topology

- **1 Layer 3 switch** performing inter-VLAN routing (router-on-a-stick or SVI-based)
- **3 access-layer switches**, one per department
- **3 VLANs**: Sales (VLAN 10), HR (VLAN 20), IT (VLAN 30)
- End-user PCs in each department, plus a DHCP server device

## IP Addressing Scheme
Subnetted using VLSM from a single 192.168.1.0/24 block to match each department's actual host requirements.

| Department/VLAN | VLAN ID | Network Address | Subnet Mask | Usable Range | Gateway |
|---|---|---|---|---|---|
| Sales (50 hosts) | 10 | 192.168.1.0 | /26 | .1 – .62 | 192.168.1.1 |
| HR (25 hosts) | 20 | 192.168.1.64 | /27 | .65 – .94 | 192.168.1.65 |
| IT (10 hosts) | 30 | 192.168.1.96 | /28 | .97 – .110 | 192.168.1.97 |

## Configuration Steps

1. **Subnetting (VLSM)** — Divided the 192.168.1.0/24 block into right-sized subnets per department based on host count, avoiding wasted IP space.
2. **VLAN Creation** — Created VLANs 10, 20, and 30 on all access switches and assigned department ports accordingly.
3. **Trunking** — Configured 802.1Q trunk links between access switches and the Layer 3 switch.
4. **Inter-VLAN Routing** — Configured SVIs on the Layer 3 switch for each VLAN, enabling controlled routing between departments.
5. **DHCP** — Configured DHCP pools per VLAN so each department's devices receive IP addresses automatically, with the correct default gateway and DNS server.
6. **Basic Security** — Set up a simple standard ACL to restrict IT-only devices (e.g. servers) from being accessed directly by the Sales VLAN.

## Key Configuration Snippets

```
! VLAN and SVI configuration
interface Vlan10
 ip address 192.168.1.1 255.255.255.192
!
interface Vlan20
 ip address 192.168.1.65 255.255.255.224
!
interface Vlan30
 ip address 192.168.1.97 255.255.255.240

! DHCP pool for Sales VLAN
ip dhcp excluded-address 192.168.1.1 192.168.1.10
ip dhcp pool SALES
 network 192.168.1.0 255.255.255.192
 default-router 192.168.1.1
 dns-server 8.8.8.8

! Trunk port on access switch
interface FastEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
```

## Verification
- `show vlan brief` — confirms VLANs are created and correct ports assigned
- `show ip interface brief` — confirms SVIs are up and have correct IP addresses
- `ping` between hosts in different VLANs — confirms inter-VLAN routing works
- `show ip dhcp binding` — confirms DHCP is assigning correct addresses per subnet
- `show access-lists` — confirms ACL is applied and matching expected traffic

## What I Learned
This project helped me understand how VLSM allows more efficient use of IP address space compared to fixed-size subnetting, especially when departments have very different host counts. I also learned how router-on-a-stick and SVI-based inter-VLAN routing differ, and how DHCP pools map directly to VLAN subnets to automate address assignment across a growing network.


---
**Author:** Noel Binu
**Certification:** CCNA (Cisco Certified Network Associate)
**Tools:** Cisco Packet Tracer / GNS3
