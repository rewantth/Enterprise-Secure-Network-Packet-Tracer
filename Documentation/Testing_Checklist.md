# Testing Checklist

## Testing Notes

- DHCP addresses may vary. Use the actual IP shown by `ipconfig` on each PC.
- Suggested addresses below assume DHCP starts after the excluded range, such as `.21`.
- Record the actual result and screenshot filename after each test.

## Connectivity and Security Test Matrix

| Test # | Source Device and IP | Destination Device and IP | Test Command | Expected Result | Actual Result Placeholder | Screenshot Required | Related Security Control |
|---:|---|---|---|---|---|---|---|
| 1 | `Admin-PC1` expected 192.168.10.21 | `Finance-PC1` expected 192.168.40.21 | `ping 192.168.40.21` | Success | `TBD` | Yes | `ADMIN_IN`, `FINANCE_IN` return allowance |
| 2 | `HR-PC1` expected 192.168.20.21 | `Finance-PC1` expected 192.168.40.21 | `ping 192.168.40.21` | Fail | `TBD` | Yes | `HR_IN` deny to Finance |
| 3 | `Guest-PC1` expected 192.168.50.21 | `Admin-PC1` expected 192.168.10.21 | `ping 192.168.10.21` | Fail | `TBD` | Yes | `GUEST_IN` internal deny |
| 4 | `Guest-PC1` expected 192.168.50.21 | `Finance-PC1` expected 192.168.40.21 | `ping 192.168.40.21` | Fail | `TBD` | Yes | `GUEST_IN` internal deny |
| 5 | `Guest-PC1` expected 192.168.50.21 | `Web-Server` 192.168.60.20 | Web browser: `http://192.168.60.20` | Success if HTTP policy is enabled | `TBD` | Yes | `GUEST_IN` Web permit, `DMZ_IN` Web reply permit |
| 6 | `IT-PC1` expected 192.168.30.21 | `Edge-Router` 192.168.99.254 or 10.0.0.1 | `ssh -l netadmin 192.168.99.254` | Success | `TBD` | Yes | `IT_IN`, `SSH_IT_ONLY`, SSHv2 |
| 7 | `HR-PC1` expected 192.168.20.21 | `Edge-Router` 192.168.99.254 | `ssh -l netadmin 192.168.99.254` | Fail | `TBD` | Yes | `HR_IN`, `SSH_IT_ONLY` |
| 8 | `Finance-PC1` expected 192.168.40.21 | `Web-Server` 192.168.60.20 | Web browser: `http://192.168.60.20` | Success | `TBD` | Yes | `FINANCE_IN` Web permit, `DMZ_IN` Web reply permit |
| 9 | Each user PC | Core DHCP service | `ipconfig` on PC; `show ip dhcp binding` on core | Correct VLAN IP assigned | `TBD` | Yes | DHCP pools and VLAN access ports |
| 10 | `Core-SW` | Access switches | `show interfaces trunk` | Trunks are operational and allow expected VLANs | `TBD` | Yes | 802.1Q trunking |
| 11 | Same VLAN PCs | Same VLAN peer | Example: `Admin-PC1 ping Admin-PC2` | Success | `TBD` | Yes | VLAN access-port assignment |
| 12 | Different VLAN PCs | Policy-controlled destination | Example: `HR-PC1 ping Finance-PC1` | Follows ACL policy | `TBD` | Yes | Named extended ACLs |
| 13 | Any access switch | Unused ports | `show interfaces status` and `show vlan brief` | Unused ports are shut down in VLAN 999 | `TBD` | Yes | VLAN 999, shutdown |
| 14 | Test unauthorized device behavior | Protected access port | Move a third device to a secured port, then run `show port-security interface Fa0/1` | Violation count increases or port restricts/shuts | `TBD` | Yes | Port security |
| 15 | `Core-SW` / `Edge-Router` | OSPF peer | `show ip ospf neighbor` | Neighbor established | `TBD` | Yes | OSPF Area 0 |
| 16 | Network devices | Running configuration | Required show commands below | Outputs verify final state | `TBD` | Yes | Full configuration validation |

## Required Show / Verification Commands

Run these on the appropriate devices and capture screenshots where listed.

```text
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
show ip ospf neighbor
show access-lists
show running-config
show port-security
show port-security interface FastEthernet0/1
show users
show ip ssh
show line vty 0 4
show ip dhcp binding
show spanning-tree
show interfaces status
```

## Allowed Traffic Tests

| Source | Destination | Command / Action | Expected Result | Evidence |
|---|---|---|---|---|
| `Admin-PC1` | `Finance-PC1` | `ping <Finance-PC1-IP>` | Success | Screenshot of successful ping |
| `IT-PC1` | `Edge-Router` | `ssh -l netadmin 192.168.99.254` | Success | Screenshot of successful SSH login |
| `IT-PC1` | `Core-SW` | `ssh -l netadmin 192.168.99.1` | Success | Screenshot of successful SSH login |
| `Finance-PC1` | `Web-Server` | Browser to `http://192.168.60.20` | Success | Screenshot of web page |
| `Guest-PC1` | `Web-Server` | Browser to `http://192.168.60.20` | Success | Screenshot of web page |
| `Guest-PC1` | `DNS-Server` | Browser to `http://www.securewave.local` | Success if DNS configured | Screenshot of resolved web page |
| Same VLAN PCs | Same VLAN peer | `ping <peer-IP>` | Success | Screenshot of ping |

## Blocked Traffic Tests

| Source | Destination | Command / Action | Expected Result | Evidence |
|---|---|---|---|---|
| `HR-PC1` | `Finance-PC1` | `ping <Finance-PC1-IP>` | Fail | Screenshot of failed ping |
| `Guest-PC1` | `Admin-PC1` | `ping <Admin-PC1-IP>` | Fail | Screenshot of failed ping |
| `Guest-PC1` | `Finance-PC1` | `ping <Finance-PC1-IP>` | Fail | Screenshot of failed ping |
| `HR-PC1` | `Edge-Router` | `ssh -l netadmin 192.168.99.254` | Fail | Screenshot of failed SSH attempt |
| `Admin-PC1` | `Core-SW` | `ssh -l netadmin 192.168.99.1` | Fail | Screenshot of failed SSH attempt |
| `DNS-Server` | `Finance-PC1` | `ping <Finance-PC1-IP>` | Fail | Screenshot or ACL hit counter |

## Verification Command Checklist

| Area | Device | Command | Expected Result |
|---|---|---|---|
| VLANs | Core and access switches | `show vlan brief` | VLANs 10,20,30,40,50,60,99,999 exist where needed. |
| Trunks | Core and access switches | `show interfaces trunk` | Expected trunks are active and allow correct VLANs. |
| SVIs | Core switch | `show ip interface brief` | VLAN interfaces are up/up when active ports exist. |
| Routing | Core and edge | `show ip route` | Connected VLANs and OSPF/default routes appear. |
| OSPF | Core and edge | `show ip ospf neighbor` | Neighbor relationship is established. |
| ACLs | Core switch | `show access-lists` | ACLs appear and counters increase during tests. |
| DHCP | Core switch | `show ip dhcp binding` | PCs receive correct VLAN IPs. |
| SSH | Routers/switches | `show ip ssh` | SSH version 2 enabled. |
| VTY | Routers/switches | `show line vty 0 4` | VTY lines configured for SSH. |
| Port security | Access switches | `show port-security` | Secure ports are listed. |
| Port security detail | Access switches | `show port-security interface FastEthernet0/1` | Max MACs, violation mode, and sticky MAC status visible. |
| STP | Switches | `show spanning-tree` | STP active; access ports use PortFast where configured. |
| Interfaces | Switches | `show interfaces status` | Used ports connected; unused ports disabled. |
