# ACL Rules Explanation

## ACL Placement Summary

| ACL Name | Purpose | Interface Applied To | Direction | Why Placed There |
|---|---|---|---|---|
| `ADMIN_IN` | Control Admin-sourced traffic and block Admin from Management VLAN. | `Core-SW interface Vlan10` | Inbound | Close to Admin traffic source. |
| `HR_IN` | Block HR from Finance and unnecessary internal networks while allowing DMZ services. | `Core-SW interface Vlan20` | Inbound | Close to HR traffic source. |
| `IT_IN` | Permit IT administrative reachability. | `Core-SW interface Vlan30` | Inbound | Close to IT source and easy to audit. |
| `FINANCE_IN` | Protect Finance from becoming a general source network while allowing DMZ services and replies. | `Core-SW interface Vlan40` | Inbound | Close to Finance traffic source. |
| `GUEST_IN` | Isolate guests from internal networks and allow only approved DMZ services. | `Core-SW interface Vlan50` | Inbound | Blocks guest traffic before it crosses the network. |
| `DMZ_IN` | Stop DMZ servers from initiating into internal VLANs while allowing service replies. | `Core-SW interface Vlan60` | Inbound | Protects internal VLANs from DMZ-initiated traffic. |
| `MGMT_IN` | Limit management VLAN sourced traffic. | `Core-SW interface Vlan99` | Inbound | Prevents management services from becoming a general access path. |
| `SSH_IT_ONLY` | Restrict device SSH access to IT users. | Router and switch VTY lines | Inbound access-class | Protects the management plane. |

## ACL: ADMIN_IN

Purpose: Allows Admin users to access HR, IT, Finance, and DMZ while blocking Management VLAN access.

Interface and direction: `Core-SW interface Vlan10`, inbound.

Full rule list:

```text
permit udp any eq bootpc any eq bootps
deny ip 192.168.10.0 0.0.0.255 192.168.99.0 0.0.0.255
permit ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255
permit ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
permit ip 192.168.10.0 0.0.0.255 192.168.40.0 0.0.0.255
permit ip 192.168.10.0 0.0.0.255 192.168.60.0 0.0.0.255
permit ip 192.168.10.0 0.0.0.255 any
```

Beginner-friendly explanation: Admin can reach business networks it is allowed to use, but it cannot connect to device management addresses.

Professional explanation: This ACL permits Admin-sourced business traffic while preserving management-plane separation by denying VLAN 99 before broader permits.

Expected test result: Admin to Finance ping succeeds. Admin to Management SSH fails unless sourced from IT.

## ACL: HR_IN

Purpose: Allows HR to reach approved DMZ services and blocks HR from Finance, Management, and unnecessary internal VLANs.

Interface and direction: `Core-SW interface Vlan20`, inbound.

Full rule list:

```text
permit udp any eq bootpc any eq bootps
permit icmp 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255 echo-reply
permit tcp 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255 established
permit icmp 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255 echo-reply
permit tcp 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255 established
permit udp 192.168.20.0 0.0.0.255 host 192.168.60.10 eq domain
permit tcp 192.168.20.0 0.0.0.255 host 192.168.60.10 eq domain
permit tcp 192.168.20.0 0.0.0.255 host 192.168.60.20 eq www
permit icmp 192.168.20.0 0.0.0.255 192.168.60.0 0.0.0.255 echo
deny ip 192.168.20.0 0.0.0.255 192.168.40.0 0.0.0.255
deny ip 192.168.20.0 0.0.0.255 192.168.99.0 0.0.0.255
deny ip 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
deny ip 192.168.20.0 0.0.0.255 192.168.30.0 0.0.0.255
deny ip 192.168.20.0 0.0.0.255 192.168.0.0 0.0.255.255
permit ip 192.168.20.0 0.0.0.255 any
```

Beginner-friendly explanation: HR can use web and DNS services in the DMZ, but HR cannot reach Finance or management systems.

Professional explanation: HR receives service-specific DMZ permissions, return-flow exceptions for stateless ACL behavior, and explicit internal network denies before optional internet permit.

Expected test result: HR to Finance ping fails. HR to Web Server HTTP succeeds.

## ACL: IT_IN

Purpose: Allows IT users to access all internal, DMZ, management, and optional internet destinations.

Interface and direction: `Core-SW interface Vlan30`, inbound.

Full rule list:

```text
permit udp any eq bootpc any eq bootps
permit ip 192.168.30.0 0.0.0.255 any
```

Beginner-friendly explanation: IT is the trusted support VLAN and can reach all areas needed for administration and troubleshooting.

Professional explanation: IT is treated as the privileged administrative source VLAN. SSH access is still separately controlled through VTY `access-class` restrictions on each device.

Expected test result: IT can SSH into the edge router, core switch, and access switches.

## ACL: FINANCE_IN

Purpose: Allows Finance to access DMZ services but blocks unnecessary Finance-initiated access to internal networks.

Interface and direction: `Core-SW interface Vlan40`, inbound.

Full rule list:

```text
permit udp any eq bootpc any eq bootps
permit icmp 192.168.40.0 0.0.0.255 192.168.10.0 0.0.0.255 echo-reply
permit tcp 192.168.40.0 0.0.0.255 192.168.10.0 0.0.0.255 established
permit icmp 192.168.40.0 0.0.0.255 192.168.30.0 0.0.0.255 echo-reply
permit tcp 192.168.40.0 0.0.0.255 192.168.30.0 0.0.0.255 established
permit udp 192.168.40.0 0.0.0.255 host 192.168.60.10 eq domain
permit tcp 192.168.40.0 0.0.0.255 host 192.168.60.10 eq domain
permit tcp 192.168.40.0 0.0.0.255 host 192.168.60.20 eq www
permit icmp 192.168.40.0 0.0.0.255 192.168.60.0 0.0.0.255 echo
deny ip 192.168.40.0 0.0.0.255 192.168.20.0 0.0.0.255
deny ip 192.168.40.0 0.0.0.255 192.168.50.0 0.0.0.255
deny ip 192.168.40.0 0.0.0.255 192.168.99.0 0.0.0.255
deny ip 192.168.40.0 0.0.0.255 192.168.10.0 0.0.0.255
deny ip 192.168.40.0 0.0.0.255 192.168.30.0 0.0.0.255
deny ip 192.168.40.0 0.0.0.255 192.168.0.0 0.0.255.255
permit ip 192.168.40.0 0.0.0.255 any
```

Beginner-friendly explanation: Finance can use the DMZ Web and DNS services. Admin and IT can reach Finance, but Finance is not allowed to freely browse internal VLANs.

Professional explanation: This ACL supports sensitive-zone protection by limiting Finance as a source while permitting return traffic required by approved Admin/IT-initiated sessions.

Expected test result: Admin to Finance ping succeeds, HR to Finance ping fails, Finance to Web Server succeeds.

## ACL: GUEST_IN

Purpose: Isolates guest users and allows only approved DMZ services.

Interface and direction: `Core-SW interface Vlan50`, inbound.

Full rule list:

```text
permit udp any eq bootpc any eq bootps
permit udp 192.168.50.0 0.0.0.255 host 192.168.60.10 eq domain
permit tcp 192.168.50.0 0.0.0.255 host 192.168.60.10 eq domain
permit tcp 192.168.50.0 0.0.0.255 host 192.168.60.20 eq www
deny ip 192.168.50.0 0.0.0.255 192.168.0.0 0.0.255.255
deny ip 192.168.50.0 0.0.0.255 10.0.0.0 0.255.255.255
permit ip 192.168.50.0 0.0.0.255 any
```

Beginner-friendly explanation: Guests can use DNS and the Web Server, but they cannot reach employee networks or management devices.

Professional explanation: Guest traffic is constrained to explicit DMZ services and optional internet egress, with broad internal RFC1918-deny controls before the final permit.

Expected test result: Guest to Admin fails, Guest to Finance fails, Guest Web access to 192.168.60.20 succeeds.

## ACL: DMZ_IN

Purpose: Prevents DMZ servers from initiating internal access while permitting replies for approved services.

Interface and direction: `Core-SW interface Vlan60`, inbound.

Full rule list:

```text
permit udp host 192.168.60.10 eq domain 192.168.0.0 0.0.255.255
permit tcp host 192.168.60.10 eq domain 192.168.0.0 0.0.255.255
permit tcp host 192.168.60.20 eq www 192.168.0.0 0.0.255.255
permit udp host 192.168.60.30 eq 1812 192.168.99.0 0.0.0.255
permit udp host 192.168.60.30 eq 1813 192.168.99.0 0.0.0.255
permit tcp host 192.168.60.30 eq 49 192.168.99.0 0.0.0.255
permit icmp 192.168.60.0 0.0.0.255 192.168.0.0 0.0.255.255 echo-reply
deny ip 192.168.60.0 0.0.0.255 192.168.0.0 0.0.255.255
permit ip 192.168.60.0 0.0.0.255 any
```

Beginner-friendly explanation: Servers can reply when someone connects to their DNS or web services, and the lab AAA server can reply to management devices if external AAA is tested. DMZ hosts cannot start general connections into internal networks.

Professional explanation: This ACL creates a stateless DMZ control model by permitting service-source replies, optional AAA response flows to management devices, and ICMP echo-replies, then denying broad DMZ-initiated internal traffic.

Expected test result: DMZ server ping to Finance fails; `show access-lists DMZ_IN` shows deny counters after testing.

## ACL: MGMT_IN

Purpose: Controls traffic sourced from the Management VLAN.

Interface and direction: `Core-SW interface Vlan99`, inbound.

Full rule list:

```text
permit tcp 192.168.99.0 0.0.0.255 eq 22 192.168.30.0 0.0.0.255
permit icmp 192.168.99.0 0.0.0.255 192.168.30.0 0.0.0.255 echo-reply
deny ip 192.168.99.0 0.0.0.255 192.168.0.0 0.0.255.255
permit ip 192.168.99.0 0.0.0.255 any
```

Beginner-friendly explanation: Management devices are allowed to answer IT SSH traffic, but Management is not a general-purpose user network.

Professional explanation: This ACL allows management-plane response traffic while blocking VLAN 99 from becoming a path into user VLANs.

Expected test result: IT SSH succeeds; non-IT SSH attempts fail because of VTY restrictions and source VLAN policy.

## ACL: SSH_IT_ONLY

Purpose: Restricts remote device administration to the IT VLAN.

Interface and direction: Applied under `line vty 0 4` and `line vty 5 15` using `access-class SSH_IT_ONLY in`.

Full rule list:

```text
permit 192.168.30.0 0.0.0.255
deny any
```

Beginner-friendly explanation: Only IT users can open SSH sessions to routers and switches.

Professional explanation: The VTY access-class protects the management plane independently of routed data-plane ACLs.

Expected test result: IT SSH login succeeds; HR SSH login fails.

## Packet Tracer Limitation

[PACKET TRACER LIMITATION]

Real-world feature: Stateful firewall policy with session tracking and identity-aware rules.

Why Packet Tracer may not fully support it: Router and switch ACLs are stateless and some advanced syntax may vary by device image.

Closest alternative: Use named extended ACLs, place them close to the source, document rule intent, and validate behavior using pings, web tests, SSH tests, and ACL hit counters.
