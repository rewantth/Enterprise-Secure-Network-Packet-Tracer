# Blocked Traffic Tests

Use this file to record evidence for traffic that should be denied by the SecureWave Solutions branch design.

## Denied Communication Evidence Table

| Test | Source | Destination | Command Used | Expected Failure | Actual Result | Screenshot Placeholder | ACL / Security Control Responsible |
|---:|---|---|---|---|---|---|---|
| 1 | `HR-PC1` | `Finance-PC1` | `ping <Finance-PC1-IP>` | Yes | `TBD` | `Screenshots/12_hr_to_finance_blocked.png` | `HR_IN` denies HR to Finance. |
| 2 | `Guest-PC1` | `Admin-PC1` | `ping <Admin-PC1-IP>` | Yes | `TBD` | `Screenshots/13_guest_to_admin_blocked.png` | `GUEST_IN` denies Guest to internal networks. |
| 3 | `Guest-PC1` | `Finance-PC1` | `ping <Finance-PC1-IP>` | Yes | `TBD` | `Screenshots/guest_to_finance_blocked.png` | `GUEST_IN` denies Guest to internal networks. |
| 4 | `Guest-PC1` | `Core-SW` | `ping 192.168.99.1` | Yes | `TBD` | `Screenshots/guest_to_management_blocked.png` | `GUEST_IN` denies Guest to Management VLAN. |
| 5 | `HR-PC1` | `Edge-Router` | `ssh -l netadmin 192.168.99.254` | Yes | `TBD` | `Screenshots/16_non_it_ssh_blocked.png` | `SSH_IT_ONLY` VTY access-class and `HR_IN`. |
| 6 | `Admin-PC1` | `Core-SW` | `ssh -l netadmin 192.168.99.1` | Yes | `TBD` | `Screenshots/admin_ssh_management_blocked.png` | `ADMIN_IN` and `SSH_IT_ONLY`. |
| 7 | `DNS-Server` | `Finance-PC1` | `ping <Finance-PC1-IP>` | Yes | `TBD` | `Screenshots/dmz_to_finance_blocked.png` | `DMZ_IN` denies DMZ initiation into internal VLANs. |
| 8 | Unknown third endpoint | Secured access port | Connect device, then run `show port-security interface Fa0/1` | Yes | `TBD` | `Screenshots/25_show_port_security_interface.png` | Port security maximum MAC and violation mode. |

## How to Prove Blocking with ACL Counters

After attempting denied traffic, run this on `Core-SW`:

```text
show access-lists
```

Look for hit counts increasing on deny entries such as:

```text
deny ip 192.168.20.0 0.0.0.255 192.168.40.0 0.0.0.255
deny ip 192.168.50.0 0.0.0.255 192.168.0.0 0.0.255.255
deny ip 192.168.60.0 0.0.0.255 192.168.0.0 0.0.255.255
```

## Professional Explanation

Blocked tests demonstrate that segmentation is actively enforced. The design does not rely only on separate IP subnets or VLAN names. ACLs, VTY restrictions, and port security are used to create measurable enforcement points.

## Evidence Notes

For failed pings, capture at least three failed attempts. For failed SSH, capture the attempted command and timeout or rejection. For ACL counters, capture `show access-lists` immediately after generating denied traffic.
