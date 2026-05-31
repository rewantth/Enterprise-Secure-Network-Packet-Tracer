# Project Report

## 1. Executive Summary

This project designs and documents a secure enterprise branch network for SecureWave Solutions. The simulated network uses Cisco Packet Tracer to model a realistic deployment with department VLANs, a DMZ server network, a guest wireless network, and a dedicated management VLAN.

The design emphasizes least privilege and defense in depth. Users are segmented by business function, network devices are managed only through SSH, access to sensitive VLANs is restricted with ACLs, servers are placed in a DMZ, guest users are isolated from internal systems, and access-layer switch ports are hardened with port security.

This is a hands-on enterprise network security simulation. It is not presented as real employment experience or a school lab. It is designed to demonstrate practical junior network/security engineering skills in a professional portfolio format.

## 2. Business Scenario

SecureWave Solutions is opening a branch office with multiple departments and shared network infrastructure. The company needs to protect sensitive Admin and Finance systems, support normal business communication, expose limited server services, and provide guest wireless access without allowing guest users to reach internal systems.

The company also wants secure network-device administration. Only IT administrators should be able to access router and switch management interfaces, and insecure protocols such as Telnet should not be used.

## 3. Network Requirements

- Use one edge router for WAN routing, optional NAT/PAT, and default route advertisement.
- Use one multilayer core switch for inter-VLAN routing.
- Use five Layer 2 access switches.
- Place every department in a separate VLAN.
- Place DNS, Web, and lab AAA servers in a DMZ VLAN.
- Place management interfaces in VLAN 99.
- Place unused ports in VLAN 999 and shut them down.
- Use DHCP for user VLANs.
- Use static addressing for servers and infrastructure.
- Use OSPF Area 0 between the core switch and edge router.
- Use trunk links between the core switch and access switches.
- Use a routed link between the core switch and edge router.

## 4. Security Requirements

- Guest users must be blocked from internal VLANs.
- Guest users may access only approved DMZ DNS and Web services.
- HR must not access Finance.
- Finance must not be reachable from HR or Guest.
- IT must be the only user VLAN allowed to SSH into routers and switches.
- Telnet must be disabled.
- SSH version 2 must be enabled.
- Local usernames must use encrypted secrets.
- Enable secrets must be used instead of enable passwords.
- Console and VTY access must be protected.
- Legal warning banners must be configured.
- Port security must be enabled on access ports.
- Unused ports must be shut down and assigned to VLAN 999.
- Management VLAN 99 must be separated from user VLANs.
- Named extended ACLs must be used for routed access-control policy enforcement.

## 5. Topology Design

The topology follows a small enterprise branch model:

- The edge router connects to the core switch using a routed /30 link.
- The core switch performs inter-VLAN routing using SVIs.
- Access switches connect to the core through trunk links.
- Department endpoints connect to access ports.
- Guest wireless connects as a bridge/AP into VLAN 50.
- DNS, Web, and lab AAA servers connect to access ports in VLAN 60.
- Network-device management addresses use VLAN 99.

This design is more realistic than a flat network because it separates business functions and security zones while keeping routing centralized at the core layer.

## 6. VLAN Design

| VLAN | Name | Purpose |
|---:|---|---|
| 10 | ADMIN | Administrative users and business operations. |
| 20 | HR | Human resources users. |
| 30 | IT | IT administrators and support users. |
| 40 | FINANCE | Sensitive finance users and systems. |
| 50 | GUEST | Guest wireless users. |
| 60 | DMZ_SERVERS | DNS, Web, and Packet Tracer lab AAA services exposed to approved clients. |
| 99 | MANAGEMENT | Switch management SVIs. |
| 999 | BLACK_HOLE_UNUSED | Disabled and unused switch ports. |

## 7. IP Addressing

| Network | Subnet | Gateway / Device IP |
|---|---|---|
| Core-to-edge routed link | 10.0.0.0/30 | Edge 10.0.0.1, Core 10.0.0.2 |
| ADMIN | 192.168.10.0/24 | 192.168.10.1 |
| HR | 192.168.20.0/24 | 192.168.20.1 |
| IT | 192.168.30.0/24 | 192.168.30.1 |
| FINANCE | 192.168.40.0/24 | 192.168.40.1 |
| GUEST | 192.168.50.0/24 | 192.168.50.1 |
| DMZ_SERVERS | 192.168.60.0/24 | 192.168.60.1 |
| MANAGEMENT | 192.168.99.0/24 | 192.168.99.1 |
| Optional ISP link | 203.0.113.0/30 | ISP 203.0.113.1, Edge 203.0.113.2 |

## 8. Interface Mapping Summary

Core switch uplinks:

- `Core-SW Gi0/1` to `Edge-Router Gi0/0`: routed link, 10.0.0.0/30.
- `Core-SW Fa0/1` to `Admin-SW Fa0/24`: trunk for VLANs 10, 99, 999.
- `Core-SW Fa0/2` to `HR-SW Fa0/24`: trunk for VLANs 20, 99, 999.
- `Core-SW Fa0/3` to `IT-SW Fa0/24`: trunk for VLANs 30, 99, 999.
- `Core-SW Fa0/4` to `Finance-SW Fa0/24`: trunk for VLANs 40, 99, 999.
- `Core-SW Fa0/5` to `DMZ-Server-SW Fa0/24`: trunk for VLANs 50, 60, 99, 999.

The complete mapping is documented in `Architecture/Interface_Mapping.md`.

## 9. Routing Design

The core switch uses SVIs as the default gateways for all VLANs. The command `ip routing` enables Layer 3 routing on the multilayer switch.

The edge router and core switch run OSPF Area 0 across the routed /30 link. The edge router advertises a default route into OSPF so internal VLANs can reach the optional simulated internet path.

### Why SVIs Instead of Router-on-a-Stick

SVIs were selected because they more closely represent a scalable enterprise branch design. A Layer 3 switch can route between VLANs at switching speeds and keeps local inter-VLAN traffic off the edge router. Router-on-a-stick is documented as an alternative, but it is not used as the main design.

### Router-on-a-Stick Alternative

In a smaller design, the edge router could use subinterfaces such as `Gi0/0.10`, `Gi0/0.20`, and `Gi0/0.30` with 802.1Q encapsulation. That approach is easier for small labs but less scalable because all inter-VLAN traffic must traverse the router interface.

## 10. DHCP Design

The core switch provides DHCP for user VLANs:

- ADMIN
- HR
- IT
- FINANCE
- GUEST

Servers, switch management SVIs, and infrastructure interfaces use static IP addresses. DHCP excluded ranges prevent conflicts with gateways and reserved infrastructure addresses.

## 11. ACL Design

Named extended ACLs are applied inbound on source VLAN SVIs wherever possible. This places enforcement close to the traffic source, which reduces unnecessary traversal through the network.

| ACL Name | Applied Interface | Direction | Purpose |
|---|---|---|---|
| `ADMIN_IN` | `interface vlan 10` | Inbound | Allows Admin access to approved internal and DMZ networks while blocking Management VLAN access. |
| `HR_IN` | `interface vlan 20` | Inbound | Allows HR to DMZ services and blocks Finance, Management, and unnecessary internal access. |
| `IT_IN` | `interface vlan 30` | Inbound | Allows IT broad administrative access. |
| `FINANCE_IN` | `interface vlan 40` | Inbound | Allows Finance to DMZ services and blocks unnecessary internal access. |
| `GUEST_IN` | `interface vlan 50` | Inbound | Allows only DNS and HTTP to DMZ and blocks internal networks. |
| `DMZ_IN` | `interface vlan 60` | Inbound | Prevents DMZ servers from initiating connections into internal VLANs while allowing service replies. |
| `MGMT_IN` | `interface vlan 99` | Inbound | Limits management VLAN sourced traffic. |
| `SSH_IT_ONLY` | VTY lines | Inbound access-class | Restricts SSH administration to the IT subnet. |

[PACKET TRACER LIMITATION]

Cisco IOS ACLs are not stateful firewalls. In a production network, a firewall such as Cisco ASA or Firepower would track connection state. In Packet Tracer, selected return traffic is explicitly permitted where needed so approved tests can succeed while unauthorized initiation remains blocked.

## 12. Management Security Design

Network management is hardened with:

- Dedicated Management VLAN 99.
- Static switch management IP addresses.
- SSH version 2.
- Telnet disabled with `transport input ssh`.
- Local administrative user with `secret`.
- `enable secret`.
- `service password-encryption`.
- Console password.
- VTY login protection.
- Legal warning banner.
- VTY access restriction allowing only IT subnet sources.

## 13. AAA/SSH Design

The devices use `aaa new-model` and local authentication fallback. If the Packet Tracer AAA Server supports the selected function, centralized authentication can be simulated using the lab AAA server in the DMZ server VLAN.

AAA server placement:

- Device: `AAA-Server`
- VLAN: 60 DMZ_SERVERS
- IP: 192.168.60.30/24
- Gateway: 192.168.60.1

[PACKET TRACER LIMITATION]

Real enterprise networks often use Cisco ISE, RADIUS, or TACACS+ for centralized authentication, authorization, and accounting. Packet Tracer support varies by version. If external AAA commands are not accepted, use the provided local AAA configuration as the compatible alternative.

## 14. Port Security Design

Access ports use:

- `switchport mode access`
- Correct VLAN assignment
- `switchport port-security`
- Maximum MAC address limit
- Sticky MAC learning
- Violation mode `restrict`
- PortFast and BPDU Guard where supported

Most user-facing ports allow a maximum of two MAC addresses. The Guest AP port is an exception because multiple wireless client MAC addresses may appear behind the AP bridge.

## 15. DMZ Design

The DMZ VLAN hosts:

- DNS Server: 192.168.60.10
- Web Server: 192.168.60.20

Internal users are allowed to reach approved DMZ services based on policy. DMZ servers are not allowed to initiate connections into internal VLANs unless specifically permitted. This reduces risk if a DMZ server is compromised.

## 16. Guest Isolation Design

Guest users are placed in VLAN 50. The guest wireless device is configured as a bridge/AP by disabling its built-in DHCP service and connecting one LAN port to a VLAN 50 access switch port.

Guest users receive DHCP from the core switch and are allowed only:

- DNS to 192.168.60.10
- HTTP to 192.168.60.20
- Optional internet access through the edge router if ISP simulation is built

Guest users are denied access to Admin, HR, IT, Finance, and Management networks.

## 17. Attack/Defense Validation

| Scenario | Expected Result | Evidence |
|---|---|---|
| Guest attempts to reach Finance | Blocked by `GUEST_IN` | Failed ping or connection screenshot |
| HR attempts to reach Finance | Blocked by `HR_IN` | Failed ping screenshot |
| Non-IT user attempts SSH to router/core | Blocked by VTY ACL and VLAN ACLs | Failed SSH screenshot |
| IT user SSHs to router/core | Success | Successful SSH login screenshot |
| Unknown device connects to access port | Restricted or blocked by port security | `show port-security` output |
| DMZ server initiates to internal VLAN | Blocked by `DMZ_IN` | Failed ping or ACL hit counter |

## 18. Testing Evidence Section

Evidence should include screenshots of:

- Full topology
- VLAN table
- Interface mapping
- Core SVI configuration
- DHCP pools
- Named ACLs
- Trunk status
- OSPF neighbor state
- Successful allowed communication
- Failed blocked communication
- SSH success from IT
- SSH failure from non-IT
- Port security verification
- DHCP bindings
- Routing table
- Packet Tracer Simulation Mode flow

## 19. Troubleshooting Section

Common issues to validate:

- Wrong VLAN on access port
- Missing trunk allowed VLAN
- SVI down because no active port exists in that VLAN
- Missing `ip routing` on core switch
- Incorrect default gateway on PC or server
- DHCP excluded range too broad
- ACL applied to wrong SVI or direction
- ACL rule order incorrect
- SSH keys missing
- VTY line still allows Telnet
- OSPF network statement missing
- NAT inside/outside not assigned
- Port security violation triggered by too many MAC addresses

Detailed troubleshooting guidance is provided in `Documentation/Troubleshooting_Guide.md`.

## 20. Packet Tracer Limitations

### External AAA

[PACKET TRACER LIMITATION]

Real-world feature: Cisco ISE, TACACS+, or RADIUS with centralized identity policy.

Why limited: Packet Tracer AAA behavior and supported CLI commands vary by device image and version.

Closest alternative: Use Packet Tracer AAA Server if available, plus local AAA fallback on every device.

### Stateful Firewalling

[PACKET TRACER LIMITATION]

Real-world feature: Stateful inspection firewall using ASA, Firepower, or another NGFW.

Why limited: IOS ACLs in this project are stateless packet filters.

Closest alternative: Use named extended ACLs with explicit service rules and explicit return allowances.

### Wireless Enterprise Authentication

[PACKET TRACER LIMITATION]

Real-world feature: WPA2/WPA3 Enterprise with 802.1X and RADIUS.

Why limited: Packet Tracer wireless devices may support only simplified wireless security.

Closest alternative: Use WPA2-PSK if available and isolate the AP into VLAN 50.

### Port Security and 802.1X

[PACKET TRACER LIMITATION]

Real-world feature: 802.1X network access control with posture and identity policy.

Why limited: Packet Tracer may not fully simulate enterprise NAC workflows.

Closest alternative: Use switch port security with sticky MAC learning and violation mode restrict.

## 21. Lessons Learned

This project demonstrates that secure enterprise networking is not one control. It is a layered design. VLANs separate traffic, routing connects approved networks, ACLs enforce policy, SSH protects administration, AAA controls login, port security protects access-layer ports, and documentation makes the design understandable and repeatable.

The most important design lesson is that segmentation must be paired with clear policy. Creating VLANs alone does not secure the network. Traffic must be explicitly permitted or denied based on business need.

## 22. Conclusion

The SecureWave Solutions branch network provides a professional Packet Tracer simulation of enterprise segmentation and network security controls. The design shows practical ability to build, configure, document, test, and explain a secure branch topology using technologies commonly associated with junior network and security roles.

## 23. Server Configuration Instructions

### DNS Server

1. Click `DNS-Server`.
2. Go to `Desktop > IP Configuration`.
3. Set IP address to `192.168.60.10`.
4. Set subnet mask to `255.255.255.0`.
5. Set default gateway to `192.168.60.1`.
6. Go to `Services > DNS`.
7. Turn DNS service `On`.
8. Add an A record:
   - Name: `www.securewave.local`
   - Address: `192.168.60.20`
9. Add an A record:
   - Name: `dns.securewave.local`
   - Address: `192.168.60.10`
10. Test from an allowed PC using the web browser or command prompt.

### Web Server

1. Click `Web-Server`.
2. Go to `Desktop > IP Configuration`.
3. Set IP address to `192.168.60.20`.
4. Set subnet mask to `255.255.255.0`.
5. Set default gateway to `192.168.60.1`.
6. Set DNS server to `192.168.60.10`.
7. Go to `Services > HTTP`.
8. Turn HTTP service `On`.
9. Optional: Edit the default HTML page to say `SecureWave Solutions DMZ Web Service`.
10. Test from allowed clients using `http://192.168.60.20` or `http://www.securewave.local`.

### AAA Server

1. Click `AAA-Server`.
2. Go to `Desktop > IP Configuration`.
3. Set IP address to `192.168.60.30`.
4. Set subnet mask to `255.255.255.0`.
5. Set default gateway to `192.168.60.1`.
6. Go to `Services > AAA`.
7. Turn AAA service `On` if supported.
8. Add a user such as:
   - Username: `netadmin`
   - Password: `SwaveAdmin2026`
9. Add network devices as AAA clients if Packet Tracer requires it:
   - Client name: `Core-SW`
   - Client IP: `192.168.99.1`
   - Secret/key: `SecureWaveAAA123`
10. Repeat for switches and router management addresses as supported.

[PACKET TRACER LIMITATION]

If AAA services or router/switch AAA commands are not supported in your version, keep the local AAA configuration in the device configs. In production, this would commonly be Cisco ISE with RADIUS or TACACS+.

## 24. PC Configuration Instructions

1. Click a PC.
2. Go to `Desktop > IP Configuration`.
3. Select `DHCP`.
4. Confirm the IP address matches the correct VLAN subnet.
5. Open `Command Prompt`.
6. Run `ipconfig`.
7. Test local connectivity with `ping <same-vlan-PC-IP>`.
8. Test routed connectivity according to the testing matrix.
9. Test web access by opening `Desktop > Web Browser` and browsing to `http://192.168.60.20`.
10. From IT PCs, test SSH using `ssh -l netadmin <device-management-IP>` if supported by Packet Tracer.

## 25. Wireless Configuration Instructions

1. Place a Linksys WRT300N or compatible Packet Tracer wireless router/AP.
2. Connect one LAN Ethernet port of the wireless device to `DMZ-Server-SW Fa0/3`.
3. Do not use the wireless router Internet/WAN port for this design.
4. Configure the switch port as VLAN 50 access.
5. On the wireless device, disable DHCP if the GUI supports it.
6. Assign the wireless device a management IP in VLAN 50, such as `192.168.50.2`.
7. Set default gateway to `192.168.50.1`.
8. Configure SSID as `SecureWave-Guest`.
9. Configure WPA2-PSK if supported.
10. Connect guest laptops to the SSID.
11. Verify guest clients receive DHCP addresses from the core switch in `192.168.50.0/24`.
12. Test guest restrictions using the testing checklist.

## 26. How to Build This in Packet Tracer Step-by-Step

1. Create a new Packet Tracer workspace.
2. Add and rename all devices.
3. Cable the routed link, trunks, access ports, server links, and wireless AP link.
4. Configure the core switch hostname, VLANs, trunks, SVIs, DHCP pools, ACLs, OSPF, SSH, and management controls.
5. Configure the edge router hostname, routed interfaces, OSPF, default route, optional NAT/PAT, SSH, and local AAA fallback.
6. Configure each access switch with VLANs, trunk uplink, access ports, management SVI, default gateway, SSH, and port security.
7. Configure DMZ servers with static IP addresses and services.
8. Configure AAA server if supported.
9. Configure guest wireless as a bridged access point in VLAN 50.
10. Set all PCs and guest clients to DHCP.
11. Verify VLAN, trunk, routing, DHCP, ACL, SSH, OSPF, and port-security status.
12. Run the testing matrix.
13. Capture evidence screenshots.
14. Save the Packet Tracer file as `SecureWave_Enterprise.pkt`.
