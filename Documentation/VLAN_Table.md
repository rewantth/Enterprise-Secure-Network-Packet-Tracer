# VLAN Table

| VLAN ID | VLAN Name | Subnet | Gateway | Devices / Ports | Security Purpose |
|---:|---|---|---|---|---|
| 10 | ADMIN | 192.168.10.0/24 | 192.168.10.1 | `Admin-SW Fa0/1-Fa0/2` | Administrative user segmentation. |
| 20 | HR | 192.168.20.0/24 | 192.168.20.1 | `HR-SW Fa0/1-Fa0/2` | HR user segmentation and Finance restriction. |
| 30 | IT | 192.168.30.0/24 | 192.168.30.1 | `IT-SW Fa0/1-Fa0/2` | IT support and secure administration source VLAN. |
| 40 | FINANCE | 192.168.40.0/24 | 192.168.40.1 | `Finance-SW Fa0/1-Fa0/2` | Sensitive finance systems protected from HR and Guest. |
| 50 | GUEST | 192.168.50.0/24 | 192.168.50.1 | `DMZ-Server-SW Fa0/3`, Guest wireless clients | Isolated guest wireless access. |
| 60 | DMZ_SERVERS | 192.168.60.0/24 | 192.168.60.1 | `DMZ-Server-SW Fa0/1-Fa0/2`, `Fa0/4` | DNS, Web, and lab AAA services isolated from internal VLANs. |
| 99 | MANAGEMENT | 192.168.99.0/24 | 192.168.99.1 | Switch SVIs | Network-device management services. |
| 999 | BLACK_HOLE_UNUSED | No routed subnet | None | Disabled unused ports and trunk native VLAN | Prevents unused ports and untagged trunk traffic from landing in production VLANs. |

## Trunk VLAN Allow List

| Trunk | Allowed VLANs | Native VLAN | Reason |
|---|---|---:|---|
| `Core-SW Fa0/1` to `Admin-SW Fa0/24` | 10, 99, 999 | 999 | Admin endpoint VLAN plus management and black-hole VLAN. |
| `Core-SW Fa0/2` to `HR-SW Fa0/24` | 20, 99, 999 | 999 | HR endpoint VLAN plus management and black-hole VLAN. |
| `Core-SW Fa0/3` to `IT-SW Fa0/24` | 30, 99, 999 | 999 | IT endpoint VLAN plus management and black-hole VLAN. |
| `Core-SW Fa0/4` to `Finance-SW Fa0/24` | 40, 99, 999 | 999 | Finance endpoint VLAN plus management and black-hole VLAN. |
| `Core-SW Fa0/5` to `DMZ-Server-SW Fa0/24` | 50, 60, 99, 999 | 999 | Guest, DMZ, management, and black-hole VLANs. |

## Access Port Assignment Summary

| Switch | Ports | VLAN | Connected Devices |
|---|---|---:|---|
| `Admin-SW` | `Fa0/1-Fa0/2` | 10 | `Admin-PC1`, `Admin-PC2` |
| `HR-SW` | `Fa0/1-Fa0/2` | 20 | `HR-PC1`, `HR-PC2` |
| `IT-SW` | `Fa0/1-Fa0/2` | 30 | `IT-PC1`, `IT-PC2` |
| `Finance-SW` | `Fa0/1-Fa0/2` | 40 | `Finance-PC1`, `Finance-PC2` |
| `DMZ-Server-SW` | `Fa0/1` | 60 | `DNS-Server` |
| `DMZ-Server-SW` | `Fa0/2` | 60 | `Web-Server` |
| `DMZ-Server-SW` | `Fa0/3` | 50 | `Guest-AP` |
| `DMZ-Server-SW` | `Fa0/4` | 60 | `AAA-Server` |
| All access switches | Unused access ports | 999 | Shut down |

## Verification

Run these commands:

```text
show vlan brief
show interfaces trunk
show interfaces status
show spanning-tree
```
