# Network Design Decisions

## Why a Layer 3 Core Switch Was Used

A multilayer core switch was selected because it reflects a common enterprise branch design. Department VLANs terminate on SVIs at the core, which allows the core switch to route traffic between VLANs without forcing every internal flow through the edge router.

This makes the design cleaner and more scalable:

- Access switches stay simple Layer 2 devices.
- The core switch becomes the local routing point.
- ACL enforcement can happen at VLAN gateways.
- The edge router focuses on WAN, NAT/PAT, and route advertisement.

## Why SVIs Were Used Instead of Router-on-a-Stick

SVIs were used because they are a better fit for a branch network with multiple VLANs. Each VLAN has a logical Layer 3 gateway on the core switch:

- VLAN 10 -> 192.168.10.1
- VLAN 20 -> 192.168.20.1
- VLAN 30 -> 192.168.30.1
- VLAN 40 -> 192.168.40.1
- VLAN 50 -> 192.168.50.1
- VLAN 60 -> 192.168.60.1
- VLAN 99 -> 192.168.99.1

Router-on-a-stick is a valid smaller-lab alternative. It would place subinterfaces on the router and trunk all VLANs to that router. This project uses SVIs because they better demonstrate enterprise Layer 3 switching and reduce reliance on a single router interface for all inter-VLAN traffic.

## Why a DMZ Was Created

The DMZ separates services that may need to be accessed by multiple networks from sensitive internal user VLANs. In this project, DNS, Web, and Packet Tracer lab AAA services are hosted in VLAN 60.

The DMZ supports a safer model:

- Internal users do not need direct server placement in their department VLANs.
- Guest users can access only approved services.
- DMZ servers are prevented from initiating traffic into internal VLANs.
- A compromised server would have limited reach compared with a flat network.

## Why Guest Wireless Was Isolated

Guest users are untrusted compared with employees. They may use unmanaged personal devices, so they are placed in VLAN 50 and denied access to internal VLANs.

Guest users are allowed only:

- DNS to the DMZ DNS server
- HTTP to the DMZ Web server
- Optional internet access if the ISP simulation is built

This demonstrates a zero-trust-inspired approach: network location alone does not imply trust.

## Why the Management VLAN Was Separated

Management interfaces are high-value targets. If normal user VLANs can reach switch and router management addresses, a compromised endpoint may attempt password guessing or configuration attacks.

VLAN 99 provides a separate management network for:

- Core switch management
- Access switch management
- Secure administrative access testing

Only IT users are allowed to manage devices over SSH.

## Why OSPF Was Used

OSPF was used between the edge router and the core switch to demonstrate dynamic routing knowledge. Although this small topology could use static routes, OSPF better reflects enterprise practice and shows that the core can advertise internal VLAN networks while the edge router advertises a default route.

OSPF design:

- Single Area 0
- Core-to-edge routed link: 10.0.0.0/30
- Internal VLANs advertised from the core
- Default route originated from the edge router
- Passive interfaces used for user-facing VLANs

## Why Named ACLs Were Used

Named extended ACLs are easier to read and maintain than numbered ACLs. Names such as `GUEST_IN`, `HR_IN`, and `DMZ_IN` make the policy purpose clear.

Named ACLs support:

- Human-readable rule intent
- Easier edits
- Professional documentation
- Clear mapping between policy and interface placement

ACLs are applied close to the traffic source, usually inbound on the source VLAN SVI. This helps stop unauthorized traffic before it crosses the network.

## Why Unused Ports Are Assigned to VLAN 999

Unused switch ports are administratively shut down and assigned to VLAN 999, named `BLACK_HOLE_UNUSED`.

This reduces risk because:

- An unused active port is not left in a production VLAN.
- Accidental connections do not land in VLAN 1.
- VLAN 999 has no gateway.
- The native VLAN on trunks is also set to VLAN 999.

This is a basic but important access-layer hardening practice.

## Why DHCP Is Centralized on the Core

The core switch provides DHCP for user VLANs. This keeps IP assignment centralized and avoids relying on the guest wireless device for addressing.

DHCP is not used for:

- Servers
- Switch management addresses
- Router infrastructure addresses
- AAA server

Those systems use static addresses to make security rules, troubleshooting, and documentation predictable.

## Why the Guest Wireless Router Is Used as a Bridge

Consumer-style wireless routers often perform NAT and DHCP by default. In this project, that behavior is disabled where possible. The wireless device is connected through a LAN port and treated as an AP/bridge.

This keeps the guest network under enterprise control:

- Guest IP addresses come from the core switch.
- Guest policy is enforced on VLAN 50.
- The wireless device does not create a hidden unmanaged subnet.

[PACKET TRACER LIMITATION]

Packet Tracer wireless options vary. If the device cannot fully act like an enterprise AP, use the closest available bridge-style configuration and document the limitation.

## Why Port Security Was Included

Port security demonstrates Layer 2 access control. It limits how many MAC addresses can appear on access ports and can restrict or shut down the port when a violation occurs.

This is not a complete replacement for 802.1X, but it is a useful Packet Tracer-compatible control that shows awareness of access-layer risks.

## Real Production Enhancements

If this design were implemented in production, the next improvements would include:

- Cisco ISE for identity-based access policy
- 802.1X for port-based authentication
- Cisco ASA or Firepower firewall between zones
- EtherChannel for redundant uplink capacity
- HSRP or another first-hop redundancy protocol
- Redundant core and edge devices
- Centralized syslog
- SIEM integration
- NetFlow or telemetry
- Configuration backup and version control
- Vulnerability scanning
- Wireless controller and WPA2/WPA3 Enterprise
