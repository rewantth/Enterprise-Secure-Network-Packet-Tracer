# Security Architecture

## Network Segmentation Strategy

The network is segmented by role and trust level. Each department or security zone has its own VLAN and IP subnet.

| Zone | VLAN | Trust Level | Security Goal |
|---|---:|---|---|
| Admin | 10 | High business sensitivity | Permit authorized business access while blocking management-plane access. |
| HR | 20 | Sensitive employee data | Block Finance and unnecessary internal access. |
| IT | 30 | Administrative | Allow broad troubleshooting and device management access. |
| Finance | 40 | Highly sensitive | Permit access only from Admin and IT; allow DMZ services. |
| Guest | 50 | Untrusted | Block internal access; allow only approved DMZ services. |
| DMZ | 60 | Semi-trusted service zone | Host DNS, Web, and Packet Tracer lab AAA services; block initiation into internal VLANs. |
| Management | 99 | Highly restricted | Provide management reachability for network devices. |
| Black-hole unused | 999 | No trust | Hold disabled ports and native VLAN. |

## Least Privilege Enforcement

The design follows least privilege by allowing only the traffic needed for each source VLAN.

Examples:

- Guest users can use DNS and HTTP in the DMZ but cannot access internal VLANs.
- HR can access approved DMZ services but cannot access Finance.
- IT can manage network devices using SSH.
- Non-IT users cannot SSH into routers or switches.
- DMZ servers cannot initiate traffic into internal VLANs.

## Defense-in-Depth Layers

This project uses multiple security layers:

| Layer | Control |
|---|---|
| Physical/topology | Separate access switches and clear cabling plan. |
| Layer 2 | VLANs, trunk restrictions, port security, disabled unused ports. |
| Layer 3 | SVIs, routed edge link, OSPF route control. |
| Access control | Named extended ACLs on source VLANs. |
| Management plane | SSHv2, VTY restrictions, AAA/local fallback, Management VLAN. |
| Server placement | DNS, Web, and lab AAA services in DMZ. |
| Guest control | Guest AP bridged into isolated VLAN 50. |
| Evidence | Testing and screenshot checklist. |

## Management Plane Security

Device administration is protected with:

- `enable secret`
- Local username with `secret`
- `service password-encryption`
- SSH version 2
- RSA key generation
- `transport input ssh`
- VTY access restriction
- Console password
- Login banner
- Management VLAN 99
- Local AAA fallback

Telnet is not enabled because it sends credentials in clear text. SSH is used for encrypted remote administration.

## DMZ Isolation Design

The DMZ VLAN contains the DNS, Web, and Packet Tracer lab AAA servers. ACLs permit approved clients to reach selected services while preventing the DMZ from initiating general traffic into internal VLANs.

Allowed examples:

- Guest to DMZ DNS
- Guest to DMZ Web
- HR to DMZ Web
- Finance to DMZ Web
- Admin and IT to DMZ services

Denied examples:

- DMZ server initiating ping to Finance
- DMZ server initiating connection to Admin
- Guest accessing Finance or Management

## Guest Network Isolation

Guest users are treated as untrusted. The Guest AP is connected to a VLAN 50 access port, and guest clients receive DHCP from the central core switch.

Guest VLAN 50 ACL policy:

- Permit DHCP.
- Permit DNS to 192.168.60.10.
- Permit HTTP to 192.168.60.20.
- Deny internal networks.
- Permit optional internet traffic if NAT/PAT is configured.

## Access Control Enforcement with Named Extended ACLs

ACLs are placed inbound on source VLAN SVIs:

| Source VLAN | ACL | Interface | Direction |
|---|---|---|---|
| ADMIN | `ADMIN_IN` | `Vlan10` | Inbound |
| HR | `HR_IN` | `Vlan20` | Inbound |
| IT | `IT_IN` | `Vlan30` | Inbound |
| FINANCE | `FINANCE_IN` | `Vlan40` | Inbound |
| GUEST | `GUEST_IN` | `Vlan50` | Inbound |
| DMZ | `DMZ_IN` | `Vlan60` | Inbound |
| MANAGEMENT | `MGMT_IN` | `Vlan99` | Inbound |

This placement blocks unwanted traffic close to the source instead of allowing it to cross the network first.

## Why This Design Is Better Than a Flat Network

A flat network places all users and services in the same broadcast and security domain. That makes lateral movement easier and makes it difficult to enforce department-specific access policies.

This segmented design improves security because:

- Departments are separated.
- Sensitive networks have explicit access rules.
- Guest users are isolated.
- DMZ services are separated from internal users.
- Network management is restricted.
- Unused ports are disabled.
- ACLs provide auditable policy enforcement.

## Packet Tracer Limitations

### Stateful Inspection

[PACKET TRACER LIMITATION]

Real-world feature: Stateful firewall rules using Cisco ASA, Firepower, or another firewall platform.

Why Packet Tracer may not fully support it: IOS ACLs are stateless and do not track sessions like a firewall.

Closest alternative: Use named extended ACLs with explicit service permits, explicit denies, and limited return-traffic allowances.

### Cisco ISE / Enterprise AAA

[PACKET TRACER LIMITATION]

Real-world feature: Cisco ISE, RADIUS, or TACACS+ for centralized identity and device administration policy.

Why Packet Tracer may not fully support it: AAA support depends on the device and Packet Tracer version.

Closest alternative: Use Packet Tracer AAA Server if available and configure local AAA fallback.

### Enterprise Wireless

[PACKET TRACER LIMITATION]

Real-world feature: Controller-managed APs with WPA2/WPA3 Enterprise and 802.1X.

Why Packet Tracer may not fully support it: Wireless device features are simplified.

Closest alternative: Use a WRT300N or AP as a bridge into VLAN 50 with DHCP disabled and WPA2-PSK if supported.

## What Would Be Added in a Real Production Environment

- Cisco ISE for identity-based access and posture checks.
- ASA or Firepower firewall for stateful zone enforcement.
- EtherChannel for redundant aggregated uplinks.
- HSRP for default gateway redundancy.
- 802.1X for port-based authentication.
- Centralized syslog and SIEM collection.
- NetFlow or telemetry for traffic visibility.
- Vulnerability scanning of DMZ services.
- Configuration backup system.
- Role-based admin accounts.
- Change management process.
- Dedicated out-of-band management network.
- Wireless LAN controller with enterprise authentication.
