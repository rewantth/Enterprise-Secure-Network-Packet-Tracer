# Security Policy Matrix

## Policy Legend

- `Allowed`: Traffic is permitted by the design.
- `Denied`: Traffic is intentionally blocked.
- `Allowed, limited`: Only specific services or return traffic are allowed.

## VLAN Communication Matrix

| Source VLAN | Destination VLAN | Allowed or Denied | Reason |
|---|---|---|---|
| ADMIN | ADMIN | Allowed | Same-department communication. |
| ADMIN | HR | Allowed | Admin may access HR resources for business operations. |
| ADMIN | IT | Allowed | Admin is permitted to communicate with IT systems. |
| ADMIN | FINANCE | Allowed | Admin may access Finance based on business policy. |
| ADMIN | GUEST | Denied | No business need for Admin to initiate guest access. |
| ADMIN | DMZ_SERVERS | Allowed | Admin may use approved DMZ services. |
| ADMIN | MANAGEMENT | Denied | Only IT should access network-device management. |
| HR | ADMIN | Denied, except return traffic | HR should not initiate access to Admin systems. Return traffic is allowed for stateless ACL behavior. |
| HR | HR | Allowed | Same-department communication. |
| HR | IT | Denied, except return traffic | IT can support HR, but HR should not initiate access to IT systems. |
| HR | FINANCE | Denied | HR is explicitly blocked from Finance. |
| HR | GUEST | Denied | No business need. |
| HR | DMZ_SERVERS | Allowed, limited | HR can access approved DNS and Web services only. |
| HR | MANAGEMENT | Denied | HR cannot manage network devices. |
| IT | ADMIN | Allowed | IT support and administration. |
| IT | HR | Allowed | IT support and administration. |
| IT | IT | Allowed | Same-department communication. |
| IT | FINANCE | Allowed | IT is allowed to reach Finance for support. |
| IT | GUEST | Allowed | IT may troubleshoot guest services. |
| IT | DMZ_SERVERS | Allowed | IT may administer and test DMZ services. |
| IT | MANAGEMENT | Allowed | IT is the only VLAN allowed to manage devices over SSH. |
| FINANCE | ADMIN | Denied, except return traffic | Finance should not initiate general access to Admin. Admin-initiated sessions must receive replies. |
| FINANCE | HR | Denied | No Finance-to-HR initiation allowed. |
| FINANCE | IT | Denied, except return traffic | IT may initiate support connections to Finance. |
| FINANCE | FINANCE | Allowed | Same-department communication. |
| FINANCE | GUEST | Denied | No business need. |
| FINANCE | DMZ_SERVERS | Allowed, limited | Finance can access approved DNS and Web services. |
| FINANCE | MANAGEMENT | Denied | Finance cannot manage network devices. |
| GUEST | ADMIN | Denied | Guest users are untrusted and blocked from internal VLANs. |
| GUEST | HR | Denied | Guest users are untrusted and blocked from internal VLANs. |
| GUEST | IT | Denied | Guest users are untrusted and blocked from internal VLANs. |
| GUEST | FINANCE | Denied | Guest users must never access Finance systems. |
| GUEST | GUEST | Allowed | Same guest VLAN communication may occur unless further client isolation is added. |
| GUEST | DMZ_SERVERS | Allowed, limited | Guest can access only DNS and HTTP services. |
| GUEST | MANAGEMENT | Denied | Guest users cannot access network-device management. |
| DMZ_SERVERS | ADMIN | Denied, except service replies | DMZ servers cannot initiate into internal networks. |
| DMZ_SERVERS | HR | Denied, except service replies | DMZ servers cannot initiate into internal networks. |
| DMZ_SERVERS | IT | Denied, except service replies | DMZ servers cannot initiate into internal networks. |
| DMZ_SERVERS | FINANCE | Denied, except service replies | DMZ servers cannot initiate into internal networks. |
| DMZ_SERVERS | GUEST | Denied, except service replies | DMZ servers reply to approved guest DNS/HTTP requests. |
| DMZ_SERVERS | DMZ_SERVERS | Allowed | Same DMZ VLAN communication. |
| DMZ_SERVERS | MANAGEMENT | Denied, except optional AAA replies | DMZ servers cannot access management except lab AAA response traffic if external AAA is tested. |
| MANAGEMENT | ADMIN | Denied | Management VLAN is not a user-access VLAN. |
| MANAGEMENT | HR | Denied | Management VLAN is not a user-access VLAN. |
| MANAGEMENT | IT | Allowed, limited | SSH response traffic may return to IT. |
| MANAGEMENT | FINANCE | Denied | Management VLAN is not a user-access VLAN. |
| MANAGEMENT | GUEST | Denied | Management VLAN must remain isolated from guests. |
| MANAGEMENT | DMZ_SERVERS | Denied | Management and DMZ should remain separated. |
| MANAGEMENT | MANAGEMENT | Allowed | Management devices may communicate as needed. |

## Important Stateless ACL Note

Cisco IOS ACLs used in this Packet Tracer design are stateless. They do not automatically understand that a packet is part of an existing session.

Because of that, some ACLs explicitly allow limited reply traffic, such as:

- ICMP echo-replies from Finance to Admin or IT
- TCP replies from Web Server source port 80
- DNS replies from DNS Server source port 53
- SSH replies from management devices to IT users

In production, a stateful firewall would handle this more cleanly.
