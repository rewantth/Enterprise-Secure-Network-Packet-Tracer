# Interface Mapping

## Core, Edge, and Optional ISP Links

| Device | Interface | Connected To | Link Type | VLAN/Network | Purpose | Cable Type |
|---|---|---|---|---|---|---|
| `Edge-Router` | `Gi0/0` | `Core-SW Gi0/1` | Routed link | 10.0.0.0/30 | Core-to-edge routing and OSPF adjacency | Copper straight-through |
| `Core-SW` | `Gi0/1` | `Edge-Router Gi0/0` | Routed link | 10.0.0.0/30 | Core routed uplink using `no switchport` | Copper straight-through |
| `Edge-Router` | `Gi0/1` | `ISP-Router Gi0/0` | Routed WAN link | 203.0.113.0/30 | Optional simulated internet and NAT/PAT outside | Copper crossover or auto-MDIX |
| `ISP-Router` | `Gi0/0` | `Edge-Router Gi0/1` | Routed WAN link | 203.0.113.0/30 | Optional upstream network | Copper crossover or auto-MDIX |

## Core to Access Switch Trunks

| Device | Interface | Connected To | Link Type | VLAN/Network | Purpose | Cable Type |
|---|---|---|---|---|---|---|
| `Core-SW` | `Fa0/1` | `Admin-SW Fa0/24` | 802.1Q trunk | VLANs 10, 99, 999 | Admin access switch uplink | Copper crossover or auto-MDIX |
| `Admin-SW` | `Fa0/24` | `Core-SW Fa0/1` | 802.1Q trunk | VLANs 10, 99, 999 | Admin access switch uplink | Copper crossover or auto-MDIX |
| `Core-SW` | `Fa0/2` | `HR-SW Fa0/24` | 802.1Q trunk | VLANs 20, 99, 999 | HR access switch uplink | Copper crossover or auto-MDIX |
| `HR-SW` | `Fa0/24` | `Core-SW Fa0/2` | 802.1Q trunk | VLANs 20, 99, 999 | HR access switch uplink | Copper crossover or auto-MDIX |
| `Core-SW` | `Fa0/3` | `IT-SW Fa0/24` | 802.1Q trunk | VLANs 30, 99, 999 | IT access switch uplink | Copper crossover or auto-MDIX |
| `IT-SW` | `Fa0/24` | `Core-SW Fa0/3` | 802.1Q trunk | VLANs 30, 99, 999 | IT access switch uplink | Copper crossover or auto-MDIX |
| `Core-SW` | `Fa0/4` | `Finance-SW Fa0/24` | 802.1Q trunk | VLANs 40, 99, 999 | Finance access switch uplink | Copper crossover or auto-MDIX |
| `Finance-SW` | `Fa0/24` | `Core-SW Fa0/4` | 802.1Q trunk | VLANs 40, 99, 999 | Finance access switch uplink | Copper crossover or auto-MDIX |
| `Core-SW` | `Fa0/5` | `DMZ-Server-SW Fa0/24` | 802.1Q trunk | VLANs 50, 60, 99, 999 | DMZ and guest access switch uplink | Copper crossover or auto-MDIX |
| `DMZ-Server-SW` | `Fa0/24` | `Core-SW Fa0/5` | 802.1Q trunk | VLANs 50, 60, 99, 999 | DMZ and guest access switch uplink | Copper crossover or auto-MDIX |

## Admin Access Switch

| Device | Interface | Connected To | Link Type | VLAN/Network | Purpose | Cable Type |
|---|---|---|---|---|---|---|
| `Admin-SW` | `Fa0/1` | `Admin-PC1 Fa0` | Access | VLAN 10 | Admin endpoint | Copper straight-through |
| `Admin-SW` | `Fa0/2` | `Admin-PC2 Fa0` | Access | VLAN 10 | Admin endpoint | Copper straight-through |
| `Admin-SW` | `Vlan99` | Logical SVI | Management | 192.168.99.11/24 | Switch management IP | None |
| `Admin-SW` | `Fa0/3-Fa0/23` | Unused | Disabled access | VLAN 999 | Shut down unused ports | None |

## HR Access Switch

| Device | Interface | Connected To | Link Type | VLAN/Network | Purpose | Cable Type |
|---|---|---|---|---|---|---|
| `HR-SW` | `Fa0/1` | `HR-PC1 Fa0` | Access | VLAN 20 | HR endpoint | Copper straight-through |
| `HR-SW` | `Fa0/2` | `HR-PC2 Fa0` | Access | VLAN 20 | HR endpoint | Copper straight-through |
| `HR-SW` | `Vlan99` | Logical SVI | Management | 192.168.99.12/24 | Switch management IP | None |
| `HR-SW` | `Fa0/3-Fa0/23` | Unused | Disabled access | VLAN 999 | Shut down unused ports | None |

## IT Access Switch

| Device | Interface | Connected To | Link Type | VLAN/Network | Purpose | Cable Type |
|---|---|---|---|---|---|---|
| `IT-SW` | `Fa0/1` | `IT-PC1 Fa0` | Access | VLAN 30 | IT endpoint and SSH testing source | Copper straight-through |
| `IT-SW` | `Fa0/2` | `IT-PC2 Fa0` | Access | VLAN 30 | IT endpoint and troubleshooting source | Copper straight-through |
| `IT-SW` | `Vlan99` | Logical SVI | Management | 192.168.99.13/24 | Switch management IP | None |
| `IT-SW` | `Fa0/3-Fa0/23` | Unused | Disabled access | VLAN 999 | Shut down unused ports | None |

## Finance Access Switch

| Device | Interface | Connected To | Link Type | VLAN/Network | Purpose | Cable Type |
|---|---|---|---|---|---|---|
| `Finance-SW` | `Fa0/1` | `Finance-PC1 Fa0` | Access | VLAN 40 | Finance endpoint | Copper straight-through |
| `Finance-SW` | `Fa0/2` | `Finance-PC2 Fa0` | Access | VLAN 40 | Finance endpoint | Copper straight-through |
| `Finance-SW` | `Vlan99` | Logical SVI | Management | 192.168.99.14/24 | Switch management IP | None |
| `Finance-SW` | `Fa0/3-Fa0/23` | Unused | Disabled access | VLAN 999 | Shut down unused ports | None |

## DMZ and Guest Access Switch

| Device | Interface | Connected To | Link Type | VLAN/Network | Purpose | Cable Type |
|---|---|---|---|---|---|---|
| `DMZ-Server-SW` | `Fa0/1` | `DNS-Server Fa0` | Access | VLAN 60 | DMZ DNS service | Copper straight-through |
| `DMZ-Server-SW` | `Fa0/2` | `Web-Server Fa0` | Access | VLAN 60 | DMZ HTTP service | Copper straight-through |
| `DMZ-Server-SW` | `Fa0/3` | `Guest-AP LAN/Ethernet 1` | Access | VLAN 50 | Guest wireless bridge uplink | Copper straight-through |
| `DMZ-Server-SW` | `Fa0/4` | `AAA-Server Fa0` | Access | VLAN 60 | Packet Tracer lab AAA service | Copper straight-through |
| `Guest-AP` | `Wireless0` | `Guest-PC1 Wireless0` | Wireless | VLAN 50 | Guest client association | Wireless |
| `Guest-AP` | `Wireless0` | `Guest-PC2 Wireless0` | Wireless | VLAN 50 | Guest client association | Wireless |
| `DMZ-Server-SW` | `Vlan99` | Logical SVI | Management | 192.168.99.15/24 | Switch management IP | None |
| `DMZ-Server-SW` | `Fa0/5-Fa0/23` | Unused | Disabled access | VLAN 999 | Shut down unused ports | None |

## Management Addressing

| Device | Management Interface | IP Address | Default Gateway / Route |
|---|---|---|---|
| `Core-SW` | `Vlan99` | 192.168.99.1/24 | Layer 3 SVI |
| `Admin-SW` | `Vlan99` | 192.168.99.11/24 | 192.168.99.1 |
| `HR-SW` | `Vlan99` | 192.168.99.12/24 | 192.168.99.1 |
| `IT-SW` | `Vlan99` | 192.168.99.13/24 | 192.168.99.1 |
| `Finance-SW` | `Vlan99` | 192.168.99.14/24 | 192.168.99.1 |
| `DMZ-Server-SW` | `Vlan99` | 192.168.99.15/24 | 192.168.99.1 |
| `AAA-Server` | `Fa0` | 192.168.60.30/24 | 192.168.60.1 |
| `Edge-Router` | `Loopback99` | 192.168.99.254/32 | Advertised through OSPF |

[PACKET TRACER LIMITATION]

The edge router connects to the core using a routed link, so it does not physically sit inside VLAN 99. `Loopback99` is used as a management-plane simulation address from the management address range. In production, a router may have a dedicated out-of-band management interface, a management VRF, or a secure routed management subnet.

## Cable Type Summary

| Source Device | Source Interface | Destination Device | Destination Interface | Cable Type | Reason |
|---|---|---|---|---|---|
| `Edge-Router` | `Gi0/0` | `Core-SW` | `Gi0/1` | Straight-through | Router-to-switch link. |
| `Edge-Router` | `Gi0/1` | `ISP-Router` | `Gi0/0` | Crossover or auto-MDIX | Router-to-router Ethernet. |
| `Core-SW` | `Fa0/1` | `Admin-SW` | `Fa0/24` | Crossover or auto-MDIX | Switch-to-switch trunk. |
| `Core-SW` | `Fa0/2` | `HR-SW` | `Fa0/24` | Crossover or auto-MDIX | Switch-to-switch trunk. |
| `Core-SW` | `Fa0/3` | `IT-SW` | `Fa0/24` | Crossover or auto-MDIX | Switch-to-switch trunk. |
| `Core-SW` | `Fa0/4` | `Finance-SW` | `Fa0/24` | Crossover or auto-MDIX | Switch-to-switch trunk. |
| `Core-SW` | `Fa0/5` | `DMZ-Server-SW` | `Fa0/24` | Crossover or auto-MDIX | Switch-to-switch trunk. |
| Access switches | User/server ports | PCs/servers/AP | Endpoint NIC/LAN port | Straight-through | Endpoint-to-switch access link. |
| Admin workstation | RS-232 | Device console | Console | Console cable | Initial local setup. |
