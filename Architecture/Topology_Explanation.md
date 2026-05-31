# Topology Explanation

## Logical Topology

```text
                              Optional ISP / Internet Simulation
                                      203.0.113.0/30
                                  +------------------+
                                  |    ISP-Router    |
                                  |  Gi0/0 .1        |
                                  +---------+--------+
                                            |
                                            | Straight-through
                                            |
                                  +---------+--------+
                                  |   Edge-Router    |
                                  | Gi0/1 203.0.113.2|
                                  | Gi0/0 10.0.0.1   |
                                  +---------+--------+
                                            |
                                            | Routed link 10.0.0.0/30
                                            |
                                  +---------+--------+
                                  |     Core-SW      |
                                  |  L3 Switch/SVIs  |
                                  | Gi0/1 10.0.0.2   |
                                  +----+----+----+---+----+
                                       |    |    |        |
                     Trunk VLANs       |    |    |        | Trunk VLANs
                     10,99,999         |    |    |        | 50,60,99,999
                                       |    |    |        |
       +-------------------------------+    |    |        +------------------------------+
       |                                    |    |                                       |
+------+-------+                    +-------+----+---+                           +-------+--------+
|   Admin-SW   |                    |    HR-SW    |                           | DMZ-Server-SW |
| VLAN 10,99   |                    | VLAN 20,99  |                           | VLAN 50,60,99 |
+------+-------+                    +-------+-----+                           +-------+--------+
       |                                    |                                         |
       | Access VLAN 10                    | Access VLAN 20                          | Access VLAN 60
  +----+-----+  +--------+             +----+-----+  +------+          +------------+ +----------+ +------------+
  |Admin-PC1|  |Admin-PC2|             | HR-PC1  |  |HR-PC2|          | DNS-Server | |Web-Server| | AAA-Server |
  +----------+  +--------+             +----------+  +------+          |192.168.60.10| |.60.20   | |192.168.60.30|
                                                                        +------------+ +----------+ +------------+
                                                                                       |
                                                                                       | Access VLAN 50
                                                                                 +-----+------+
                                                                                 | Guest AP   |
                                                                                 | WRT300N    |
                                                                                 +-----+------+
                                                                                       )) wireless SSID:
                                                                                       )) SecureWave-Guest
                                                                                 +-----+------+ +------+
                                                                                 |Guest-PC1   | |Guest-PC2|
                                                                                 +------------+ +------+

                    Trunk VLANs 30,99,999                 Trunk VLANs 40,99,999
                         +----------+                         +----------+
                         |  IT-SW   |                         |Finance-SW|
                         |VLAN30,99 |                         |VLAN40,99 |
                         +----+-----+                         +----+-----+
                              |                                    |
        +---------------------+                              +-----+------+ +----------+
        |                                                    |Finance-PC1 | |Finance-PC2|
   +----+-----+  +--------+                                  +------------+ +----------+
   | IT-PC1  |  | IT-PC2 |
   +----------+  +--------+
```

## Major Device Roles

| Device | Role |
|---|---|
| `Edge-Router` | Provides WAN connectivity, default route origination, optional NAT/PAT, and SSH-managed router administration. |
| `Core-SW` | Multilayer switch that performs inter-VLAN routing using SVIs, hosts DHCP pools, applies VLAN ACLs, and runs OSPF with the edge router. |
| `Admin-SW` | Layer 2 access switch for Admin endpoints in VLAN 10 and management SVI in VLAN 99. |
| `HR-SW` | Layer 2 access switch for HR endpoints in VLAN 20 and management SVI in VLAN 99. |
| `IT-SW` | Layer 2 access switch for IT endpoints in VLAN 30 and management SVI in VLAN 99. |
| `Finance-SW` | Layer 2 access switch for Finance endpoints in VLAN 40 and management SVI in VLAN 99. |
| `DMZ-Server-SW` | Layer 2 access switch for DNS, Web, and lab AAA servers in VLAN 60 plus Guest AP bridge access in VLAN 50. |
| `Guest AP / WRT300N` | Wireless access point style device bridged into VLAN 50. DHCP is disabled so guest clients receive central DHCP from the core switch. |
| `DNS-Server` | DMZ DNS service for `securewave.local`. |
| `Web-Server` | DMZ HTTP service reachable only by approved VLANs. |
| `AAA-Server` | Optional Packet Tracer lab AAA service in the DMZ server VLAN at 192.168.60.30. |

## Trunk Links

Trunk links carry VLAN traffic between the core switch and the access switches. Each trunk allows only the VLANs needed by that access switch plus the management VLAN and black-hole VLAN.

| Trunk | Allowed VLANs | Native VLAN |
|---|---|---|
| `Core-SW Fa0/1` to `Admin-SW Fa0/24` | 10, 99, 999 | 999 |
| `Core-SW Fa0/2` to `HR-SW Fa0/24` | 20, 99, 999 | 999 |
| `Core-SW Fa0/3` to `IT-SW Fa0/24` | 30, 99, 999 | 999 |
| `Core-SW Fa0/4` to `Finance-SW Fa0/24` | 40, 99, 999 | 999 |
| `Core-SW Fa0/5` to `DMZ-Server-SW Fa0/24` | 50, 60, 99, 999 | 999 |

The native VLAN is set to VLAN 999 instead of VLAN 1. This reduces accidental use of the default VLAN and makes untagged trunk traffic land in an unused black-hole VLAN.

## Access Ports

Access ports connect endpoints to exactly one VLAN. User-facing access ports are protected with port security, sticky MAC learning, and violation mode `restrict`.

Examples:

- `Admin-SW Fa0/1` -> `Admin-PC1` -> VLAN 10
- `HR-SW Fa0/1` -> `HR-PC1` -> VLAN 20
- `IT-SW Fa0/1` -> `IT-PC1` -> VLAN 30
- `Finance-SW Fa0/1` -> `Finance-PC1` -> VLAN 40
- `DMZ-Server-SW Fa0/1` -> `DNS-Server` -> VLAN 60
- `DMZ-Server-SW Fa0/2` -> `Web-Server` -> VLAN 60
- `DMZ-Server-SW Fa0/4` -> `AAA-Server` -> VLAN 60
- `DMZ-Server-SW Fa0/3` -> `Guest AP LAN port` -> VLAN 50

## Routed Edge Link

The edge router connects to the core switch through a routed point-to-point link:

- `Edge-Router Gi0/0`: 10.0.0.1/30
- `Core-SW Gi0/1`: 10.0.0.2/30

The core switch interface uses `no switchport`, which makes it operate as a routed Layer 3 port instead of a Layer 2 switchport.

## DMZ Placement

DMZ services are placed in VLAN 60. This design keeps exposed services separate from internal user VLANs. ACLs allow approved users to reach DNS and HTTP services while preventing DMZ servers from initiating connections into internal VLANs.

## Guest Wireless Placement

Guest wireless is placed in VLAN 50. The wireless router/AP is used as a bridge, not as a separate NAT router. Its built-in DHCP server is disabled so guest clients receive addresses from the central core switch DHCP pool.

[PACKET TRACER LIMITATION]

Some Packet Tracer wireless devices do not behave exactly like enterprise lightweight APs. In a real environment, this design would use controller-based APs, WPA2/WPA3 Enterprise, and 802.1X. The Packet Tracer-compatible alternative is to bridge a WRT300N or AP into VLAN 50 and enforce policy at the core SVI.

## Cable Type Table

| Source Device | Source Interface | Destination Device | Destination Interface | Cable Type | Reason |
|---|---|---|---|---|---|
| `Edge-Router` | `Gi0/0` | `Core-SW` | `Gi0/1` | Copper straight-through | Router-to-switch Ethernet link. |
| `Edge-Router` | `Gi0/1` | `ISP-Router` | `Gi0/0` | Copper crossover or straight-through if auto-MDIX | Router-to-router Ethernet link. Packet Tracer auto-connect usually works. |
| `Core-SW` | `Fa0/1` | `Admin-SW` | `Fa0/24` | Copper crossover or straight-through if auto-MDIX | Switch-to-switch trunk. |
| `Core-SW` | `Fa0/2` | `HR-SW` | `Fa0/24` | Copper crossover or straight-through if auto-MDIX | Switch-to-switch trunk. |
| `Core-SW` | `Fa0/3` | `IT-SW` | `Fa0/24` | Copper crossover or straight-through if auto-MDIX | Switch-to-switch trunk. |
| `Core-SW` | `Fa0/4` | `Finance-SW` | `Fa0/24` | Copper crossover or straight-through if auto-MDIX | Switch-to-switch trunk. |
| `Core-SW` | `Fa0/5` | `DMZ-Server-SW` | `Fa0/24` | Copper crossover or straight-through if auto-MDIX | Switch-to-switch trunk. |
| `Admin-SW` | `Fa0/1` | `Admin-PC1` | `Fa0` | Copper straight-through | PC-to-switch access link. |
| `Admin-SW` | `Fa0/2` | `Admin-PC2` | `Fa0` | Copper straight-through | PC-to-switch access link. |
| `HR-SW` | `Fa0/1` | `HR-PC1` | `Fa0` | Copper straight-through | PC-to-switch access link. |
| `HR-SW` | `Fa0/2` | `HR-PC2` | `Fa0` | Copper straight-through | PC-to-switch access link. |
| `IT-SW` | `Fa0/1` | `IT-PC1` | `Fa0` | Copper straight-through | PC-to-switch access link. |
| `IT-SW` | `Fa0/2` | `IT-PC2` | `Fa0` | Copper straight-through | PC-to-switch access link. |
| `Finance-SW` | `Fa0/1` | `Finance-PC1` | `Fa0` | Copper straight-through | PC-to-switch access link. |
| `Finance-SW` | `Fa0/2` | `Finance-PC2` | `Fa0` | Copper straight-through | PC-to-switch access link. |
| `DMZ-Server-SW` | `Fa0/1` | `DNS-Server` | `Fa0` | Copper straight-through | Server-to-switch access link. |
| `DMZ-Server-SW` | `Fa0/2` | `Web-Server` | `Fa0` | Copper straight-through | Server-to-switch access link. |
| `DMZ-Server-SW` | `Fa0/3` | `Guest-AP` | `LAN/Ethernet 1` | Copper straight-through | AP bridge-to-switch access link. |
| `DMZ-Server-SW` | `Fa0/4` | `AAA-Server` | `Fa0` | Copper straight-through | Packet Tracer lab AAA server link. |
| `Guest-AP` | `Wireless0` | `Guest-PC1` | `Wireless0` | Wireless | Guest SSID client association. |
| `Guest-AP` | `Wireless0` | `Guest-PC2` | `Wireless0` | Wireless | Guest SSID client association. |
| Admin PC or laptop | `RS-232` | Any router/switch | `Console` | Console cable | Initial out-of-band configuration if desired. |

## Verification Commands

Use these commands after the topology is cabled and configured:

```text
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
show ip ospf neighbor
show access-lists
show port-security
show spanning-tree
show interfaces status
```
