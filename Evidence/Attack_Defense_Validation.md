# Attack/Defense Validation

This section documents small cybersecurity validation scenarios. Each scenario includes the attack attempt, the defensive control, the expected result, the screenshot required, and a professional explanation.

## Scenario 1: Unauthorized Guest User Tries to Reach Finance VLAN

| Field | Detail |
|---|---|
| Attack attempt | From `Guest-PC1`, run `ping <Finance-PC1-IP>`. |
| Source | `Guest-PC1`, expected DHCP address 192.168.50.21 |
| Target | `Finance-PC1`, expected DHCP address 192.168.40.21 |
| Security control used | `GUEST_IN` named extended ACL applied inbound on `Core-SW interface Vlan50`. |
| Expected result | Blocked. Ping should fail. |
| Evidence screenshot required | `Screenshots/guest_to_finance_blocked.png` |
| Professional explanation | Guest devices are untrusted and are prevented from reaching internal business VLANs. The ACL allows only approved DMZ DNS and HTTP services before denying internal RFC1918 traffic. |

## Scenario 2: HR User Tries to Reach Finance VLAN

| Field | Detail |
|---|---|
| Attack attempt | From `HR-PC1`, run `ping <Finance-PC1-IP>`. |
| Source | `HR-PC1`, expected DHCP address 192.168.20.21 |
| Target | `Finance-PC1`, expected DHCP address 192.168.40.21 |
| Security control used | `HR_IN` named extended ACL applied inbound on `Core-SW interface Vlan20`. |
| Expected result | Blocked. Ping should fail. |
| Evidence screenshot required | `Screenshots/12_hr_to_finance_blocked.png` |
| Professional explanation | Finance contains sensitive data and is not reachable from HR. The HR ACL explicitly denies traffic from 192.168.20.0/24 to 192.168.40.0/24. |

## Scenario 3: Non-IT User Tries SSH to Router or Core Switch

| Field | Detail |
|---|---|
| Attack attempt | From `HR-PC1`, run `ssh -l netadmin 192.168.99.254` or `ssh -l netadmin 192.168.99.1`. |
| Source | `HR-PC1`, expected DHCP address 192.168.20.21 |
| Target | `Edge-Router Loopback99` 192.168.99.254 or `Core-SW Vlan99` 192.168.99.1 |
| Security control used | `SSH_IT_ONLY` VTY access-class, `HR_IN` deny to Management VLAN, SSH-only VTY lines. |
| Expected result | Blocked. SSH should fail. |
| Evidence screenshot required | `Screenshots/16_non_it_ssh_blocked.png` |
| Professional explanation | Network-device management is restricted to the IT VLAN. Non-IT users are blocked at the VLAN policy layer and also by VTY source restrictions. |

## Scenario 4: IT User SSHs into Router or Core Switch

| Field | Detail |
|---|---|
| Attack attempt | Authorized administration test from `IT-PC1`: `ssh -l netadmin 192.168.99.1` and `ssh -l netadmin 192.168.99.254`. |
| Source | `IT-PC1`, expected DHCP address 192.168.30.21 |
| Target | `Core-SW` 192.168.99.1 and `Edge-Router` 192.168.99.254 |
| Security control used | SSHv2, `SSH_IT_ONLY` access-class permitting 192.168.30.0/24, local AAA fallback. |
| Expected result | Success. IT user should authenticate and reach device CLI. |
| Evidence screenshot required | `Screenshots/15_it_ssh_success.png` |
| Professional explanation | This validates secure administrative access. IT can manage devices over encrypted SSH, while Telnet is disabled and non-IT sources are denied. |

## Scenario 5: Unknown Device Connects to an Access Port

| Field | Detail |
|---|---|
| Attack attempt | Connect an extra endpoint to a secured user-facing access port after sticky MAC learning. |
| Source | Unknown endpoint |
| Target | Protected access switch port such as `Admin-SW Fa0/1` |
| Security control used | Switch port security with sticky MAC, max MAC limit, and violation mode `restrict`. |
| Expected result | Port restricts traffic or increments violation counter. If using `shutdown` mode, the port may err-disable. |
| Evidence screenshot required | `Screenshots/25_show_port_security_interface.png` |
| Professional explanation | Port security reduces unauthorized Layer 2 access by limiting learned MAC addresses on user-facing ports. This is not a replacement for 802.1X, but it is a Packet Tracer-compatible control. |

## Scenario 6: DMZ Server Tries to Initiate Traffic into Internal VLANs

| Field | Detail |
|---|---|
| Attack attempt | From `DNS-Server` or `Web-Server`, run `ping <Finance-PC1-IP>` or `ping <Admin-PC1-IP>`. |
| Source | `DNS-Server` 192.168.60.10 or `Web-Server` 192.168.60.20 |
| Target | Internal user VLAN endpoint |
| Security control used | `DMZ_IN` named extended ACL applied inbound on `Core-SW interface Vlan60`. |
| Expected result | Blocked unless explicitly permitted. Ping should fail. |
| Evidence screenshot required | `Screenshots/dmz_to_internal_blocked.png` and `Screenshots/08_show_access_lists.png` |
| Professional explanation | DMZ services are semi-trusted. They may respond to approved DNS and web requests, but they should not initiate connections into internal VLANs. This reduces the impact of a compromised DMZ host. |

## Summary

These scenarios prove that the network controls support both availability and security. Authorized access works, unauthorized access fails, and the evidence can be reviewed using screenshots, CLI output, and ACL hit counters.
