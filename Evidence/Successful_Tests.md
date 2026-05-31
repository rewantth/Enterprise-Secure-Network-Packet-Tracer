# Successful Tests

Use this file to record evidence for traffic that should be allowed by the SecureWave Solutions branch design.

## Allowed Communication Evidence Table

| Test | Source | Destination | Command Used | Expected Success | Actual Result | Screenshot Placeholder | Explanation |
|---:|---|---|---|---|---|---|---|
| 1 | `Admin-PC1` | `Finance-PC1` | `ping <Finance-PC1-IP>` | Yes | `TBD` | `Screenshots/11_admin_to_finance_success.png` | Admin is allowed to access Finance according to business policy. |
| 2 | `IT-PC1` | `Edge-Router` | `ssh -l netadmin 192.168.99.254` | Yes | `TBD` | `Screenshots/15_it_ssh_router_success.png` | IT is the approved management source VLAN. |
| 3 | `IT-PC1` | `Core-SW` | `ssh -l netadmin 192.168.99.1` | Yes | `TBD` | `Screenshots/15_it_ssh_core_success.png` | VTY access-class allows IT subnet sources. |
| 4 | `Finance-PC1` | `Web-Server` | Browser to `http://192.168.60.20` | Yes | `TBD` | `Screenshots/08_finance_to_web_success.png` | Finance is allowed to reach approved DMZ Web service. |
| 5 | `Guest-PC1` | `Web-Server` | Browser to `http://192.168.60.20` | Yes | `TBD` | `Screenshots/14_guest_to_web_success.png` | Guest is allowed HTTP access only to the approved DMZ Web Server. |
| 6 | `Guest-PC1` | `DNS-Server` | Browser to `http://www.securewave.local` | Yes | `TBD` | `Screenshots/guest_dns_resolution_success.png` | Guest is allowed DNS to the approved DMZ DNS Server. |
| 7 | `Admin-PC1` | `Admin-PC2` | `ping <Admin-PC2-IP>` | Yes | `TBD` | `Screenshots/admin_same_vlan_success.png` | Same-VLAN communication should work. |
| 8 | `HR-PC1` | `HR-PC2` | `ping <HR-PC2-IP>` | Yes | `TBD` | `Screenshots/hr_same_vlan_success.png` | Same-VLAN communication should work. |
| 9 | `IT-PC1` | `Finance-PC1` | `ping <Finance-PC1-IP>` | Yes | `TBD` | `Screenshots/it_to_finance_success.png` | IT is allowed to support Finance systems. |
| 10 | Any user PC | DHCP server on Core-SW | `ipconfig` | Yes | `TBD` | `Screenshots/21_pc_ipconfig_dhcp.png` | DHCP should assign an address from the correct VLAN pool. |

## Verification Commands for Successful Tests

Run these after successful tests:

```text
show ip dhcp binding
show access-lists
show ip route
show interfaces trunk
show ip ospf neighbor
show ip ssh
show users
```

## Professional Explanation

Successful tests prove that the security policy is not simply blocking everything. The design allows business-required access while denying unnecessary or risky paths. This is an important part of least privilege: approved communication should work reliably, while unauthorized communication should fail predictably.

## Evidence Notes

For each screenshot, capture:

- Source device name.
- Destination IP or URL.
- Command or browser address.
- Success output.
- If possible, related verification output such as ACL counters or DHCP bindings.
