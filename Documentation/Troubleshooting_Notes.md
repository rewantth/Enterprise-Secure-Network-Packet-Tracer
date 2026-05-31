# Troubleshooting Notes

This file is a quick-reference companion to `Troubleshooting_Guide.md`.

## Quick Checks

| Problem | First Command to Check | Most Common Cause |
|---|---|---|
| PC has wrong IP range | `ipconfig` | Access port assigned to wrong VLAN |
| PC has no DHCP address | `show ip dhcp binding` | VLAN/trunk/SVI issue or DHCP pool issue |
| Inter-VLAN ping fails | `show ip route` | `ip routing` missing or SVI down |
| Trunk not passing VLANs | `show interfaces trunk` | VLAN missing from trunk allow list |
| SVI down/down | `show ip interface brief` | No active port in that VLAN |
| HR can reach Finance | `show access-lists HR_IN` | ACL missing, wrong direction, or rule order issue |
| Guest can reach internal VLAN | `show access-lists GUEST_IN` | Guest ACL not applied to VLAN 50 inbound |
| SSH fails from IT | `show ip ssh` | RSA keys, domain name, or VTY config missing |
| SSH works from non-IT | `show running-config | section line vty` | VTY access-class missing |
| Port security violation | `show port-security interface <interface>` | Too many MAC addresses learned |
| DNS name does not resolve | DNS Server GUI and PC `ipconfig` | DNS service off or client DNS incorrect |
| Web server unreachable | Server GUI and `show access-lists` | HTTP off, wrong gateway, or ACL issue |
| OSPF neighbor missing | `show ip ospf neighbor` | Wrong IP, shutdown interface, passive interface, or area mismatch |

## Packet Tracer Build Sanity Order

1. Confirm all devices are named correctly.
2. Confirm cabling matches `Architecture/Interface_Mapping.md`.
3. Confirm VLANs exist on core and access switches.
4. Confirm trunks are up and allow expected VLANs.
5. Confirm core SVIs are up/up.
6. Confirm `ip routing` is enabled on the core switch.
7. Confirm DHCP pools exist on the core switch.
8. Confirm PCs receive DHCP addresses from the correct subnet.
9. Confirm DNS, Web, and AAA servers have static IP settings.
10. Confirm ACLs are applied inbound on the correct source VLAN SVIs.
11. Confirm SSH is enabled and Telnet is not allowed.
12. Capture screenshots after each successful verification step.

## Most Important Reminder

Do not troubleshoot ACLs first. Confirm VLANs, trunks, SVIs, gateways, DHCP, and routing before assuming the ACL is the problem.
