# IP Addressing Table

## Network Summary

| Network / VLAN | Subnet | Default Gateway | Addressing Method |
|---|---|---|---|
| Core-to-edge routed link | 10.0.0.0/30 | Not applicable | Static |
| VLAN 10 ADMIN | 192.168.10.0/24 | 192.168.10.1 | DHCP for PCs |
| VLAN 20 HR | 192.168.20.0/24 | 192.168.20.1 | DHCP for PCs |
| VLAN 30 IT | 192.168.30.0/24 | 192.168.30.1 | DHCP for PCs |
| VLAN 40 FINANCE | 192.168.40.0/24 | 192.168.40.1 | DHCP for PCs |
| VLAN 50 GUEST | 192.168.50.0/24 | 192.168.50.1 | DHCP for guest clients |
| VLAN 60 DMZ_SERVERS | 192.168.60.0/24 | 192.168.60.1 | Static for servers |
| VLAN 99 MANAGEMENT | 192.168.99.0/24 | 192.168.99.1 | Static for devices |
| VLAN 999 BLACK_HOLE_UNUSED | No routed subnet | None | No addressing |
| Optional ISP link | 203.0.113.0/30 | ISP side 203.0.113.1 | Static |

## Infrastructure Addresses

| Device | Interface | IP Address | Subnet Mask | Gateway / Next Hop | Notes |
|---|---|---:|---:|---:|---|
| `Edge-Router` | `Gi0/0` | 10.0.0.1 | 255.255.255.252 | Connected to Core | Inside routed link |
| `Core-SW` | `Gi0/1` | 10.0.0.2 | 255.255.255.252 | Connected to Edge | Routed switchport |
| `Edge-Router` | `Gi0/1` | 203.0.113.2 | 255.255.255.252 | 203.0.113.1 | Optional ISP simulation |
| `ISP-Router` | `Gi0/0` | 203.0.113.1 | 255.255.255.252 | Upstream/cloud | Optional |
| `Edge-Router` | `Loopback99` | 192.168.99.254 | 255.255.255.255 | Advertised in OSPF | Router management simulation |

## Core Switch SVI Gateways

| VLAN | Interface | IP Address | Subnet Mask | Purpose |
|---:|---|---:|---:|---|
| 10 | `Vlan10` | 192.168.10.1 | 255.255.255.0 | ADMIN gateway |
| 20 | `Vlan20` | 192.168.20.1 | 255.255.255.0 | HR gateway |
| 30 | `Vlan30` | 192.168.30.1 | 255.255.255.0 | IT gateway |
| 40 | `Vlan40` | 192.168.40.1 | 255.255.255.0 | FINANCE gateway |
| 50 | `Vlan50` | 192.168.50.1 | 255.255.255.0 | GUEST gateway |
| 60 | `Vlan60` | 192.168.60.1 | 255.255.255.0 | DMZ gateway |
| 99 | `Vlan99` | 192.168.99.1 | 255.255.255.0 | MANAGEMENT gateway |

## Switch Management Addresses

| Device | Management Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---:|---:|---:|
| `Core-SW` | `Vlan99` | 192.168.99.1 | 255.255.255.0 | Not required; Layer 3 switch |
| `Admin-SW` | `Vlan99` | 192.168.99.11 | 255.255.255.0 | 192.168.99.1 |
| `HR-SW` | `Vlan99` | 192.168.99.12 | 255.255.255.0 | 192.168.99.1 |
| `IT-SW` | `Vlan99` | 192.168.99.13 | 255.255.255.0 | 192.168.99.1 |
| `Finance-SW` | `Vlan99` | 192.168.99.14 | 255.255.255.0 | 192.168.99.1 |
| `DMZ-Server-SW` | `Vlan99` | 192.168.99.15 | 255.255.255.0 | 192.168.99.1 |

## Server Addresses

| Device | VLAN | IP Address | Subnet Mask | Default Gateway | DNS |
|---|---:|---:|---:|---:|---:|
| `DNS-Server` | 60 | 192.168.60.10 | 255.255.255.0 | 192.168.60.1 | 192.168.60.10 |
| `Web-Server` | 60 | 192.168.60.20 | 255.255.255.0 | 192.168.60.1 | 192.168.60.10 |
| `AAA-Server` | 60 | 192.168.60.30 | 255.255.255.0 | 192.168.60.1 | 192.168.60.10 |
| `Guest-AP` | 50 | 192.168.50.2 | 255.255.255.0 | 192.168.50.1 | 192.168.60.10 |

## DHCP Pool Plan

| Pool | Network | Excluded Range | Expected Client Range | Gateway | DNS |
|---|---|---|---|---|---|
| `ADMIN` | 192.168.10.0/24 | 192.168.10.1-192.168.10.20 | 192.168.10.21+ | 192.168.10.1 | 192.168.60.10 |
| `HR` | 192.168.20.0/24 | 192.168.20.1-192.168.20.20 | 192.168.20.21+ | 192.168.20.1 | 192.168.60.10 |
| `IT` | 192.168.30.0/24 | 192.168.30.1-192.168.30.20 | 192.168.30.21+ | 192.168.30.1 | 192.168.60.10 |
| `FINANCE` | 192.168.40.0/24 | 192.168.40.1-192.168.40.20 | 192.168.40.21+ | 192.168.40.1 | 192.168.60.10 |
| `GUEST` | 192.168.50.0/24 | 192.168.50.1-192.168.50.20 | 192.168.50.21+ | 192.168.50.1 | 192.168.60.10 |

## Endpoint Address Expectations

DHCP assignments may vary depending on the order in which PCs request addresses. Use `ipconfig` on each PC and `show ip dhcp binding` on `Core-SW` to record the actual address.

| Device | VLAN | Expected Address | Method |
|---|---:|---:|---|
| `Admin-PC1` | 10 | 192.168.10.21 | DHCP |
| `Admin-PC2` | 10 | 192.168.10.22 | DHCP |
| `HR-PC1` | 20 | 192.168.20.21 | DHCP |
| `HR-PC2` | 20 | 192.168.20.22 | DHCP |
| `IT-PC1` | 30 | 192.168.30.21 | DHCP |
| `IT-PC2` | 30 | 192.168.30.22 | DHCP |
| `Finance-PC1` | 40 | 192.168.40.21 | DHCP |
| `Finance-PC2` | 40 | 192.168.40.22 | DHCP |
| `Guest-PC1` | 50 | 192.168.50.21 | DHCP |
| `Guest-PC2` | 50 | 192.168.50.22 | DHCP |

## DNS Records

| Record Type | Name | IP Address | Purpose |
|---|---|---:|---|
| A | `www.securewave.local` | 192.168.60.20 | DMZ Web Server |
| A | `dns.securewave.local` | 192.168.60.10 | DMZ DNS Server |
| A | `aaa.securewave.local` | 192.168.60.30 | Optional AAA Server |
