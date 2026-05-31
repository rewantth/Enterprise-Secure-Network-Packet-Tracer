# Screenshot Checklist

Capture these screenshots after the network is configured and tested. Use clear file names so the evidence is easy to review.

| # | Screenshot | Recommended Filename | Device / Location | Notes |
|---:|---|---|---|---|
| 1 | Full topology view | `01_full_topology.png` | Packet Tracer workspace | Show all devices and labels. |
| 2 | VLAN table using `show vlan brief` | `02_show_vlan_brief.png` | Core switch or access switch | Proves VLAN creation and port assignment. |
| 3 | IP addressing table | `03_ip_addressing_table.png` | Documentation or Packet Tracer notes | Show the planned addressing table. |
| 4 | Interface mapping table | `04_interface_mapping_table.png` | Documentation or Packet Tracer notes | Show major cable/interface map. |
| 5 | Core switch SVI configuration | `05_core_svi_config.png` | `Core-SW` CLI | Show `interface vlan` sections or `show ip int brief`. |
| 6 | DHCP pool configuration | `06_dhcp_pool_config.png` | `Core-SW` CLI | Show DHCP pools and excluded addresses. |
| 7 | Named ACL configuration | `07_named_acl_config.png` | `Core-SW` CLI | Show named ACLs. |
| 8 | `show access-lists` output | `08_show_access_lists.png` | `Core-SW` CLI | Capture counters after tests. |
| 9 | `show interfaces trunk` output | `09_show_interfaces_trunk.png` | `Core-SW` CLI | Verify trunk status and allowed VLANs. |
| 10 | `show ip ospf neighbor` output | `10_show_ip_ospf_neighbor.png` | Core or edge router | Verify OSPF adjacency. |
| 11 | Successful Admin to Finance ping | `11_admin_to_finance_success.png` | `Admin-PC1` command prompt | Expected success. |
| 12 | Failed HR to Finance ping | `12_hr_to_finance_blocked.png` | `HR-PC1` command prompt | Expected failure. |
| 13 | Failed Guest to internal VLAN ping | `13_guest_to_internal_blocked.png` | `Guest-PC1` command prompt | Expected failure. |
| 14 | Successful Guest to allowed Web Server | `14_guest_to_web_success.png` | `Guest-PC1` browser | Expected success if policy allows. |
| 15 | Successful IT SSH login | `15_it_ssh_success.png` | `IT-PC1` command prompt | SSH to router or core switch. |
| 16 | Failed non-IT SSH attempt | `16_non_it_ssh_blocked.png` | `HR-PC1` or `Admin-PC1` command prompt | Expected failure. |
| 17 | Port security configuration | `17_port_security_config.png` | Access switch CLI | Show `show port-security`. |
| 18 | Unused ports shutdown and Black Hole VLAN assignment | `18_unused_ports_vlan999.png` | Access switch CLI | Use `show interfaces status` and `show vlan brief`. |
| 19 | Server DNS configuration | `19_dns_server_config.png` | DNS server GUI | Show DNS service and records. |
| 20 | Server Web configuration | `20_web_server_config.png` | Web server GUI | Show HTTP service enabled. |
| 21 | DHCP-assigned PC IP address using `ipconfig` | `21_pc_ipconfig_dhcp.png` | Any PC command prompt | Show correct VLAN IP. |
| 22 | Packet Tracer Simulation Mode packet flow test | `22_simulation_mode_flow.png` | Simulation Mode | Show successful or blocked PDU flow. |
| 23 | `show ip route` output | `23_show_ip_route.png` | Core and/or edge | Show connected, OSPF, and default routes. |
| 24 | `show ip dhcp binding` output | `24_show_ip_dhcp_binding.png` | `Core-SW` CLI | Show DHCP leases. |
| 25 | `show port-security interface` output | `25_show_port_security_interface.png` | Access switch CLI | Show specific protected interface. |

## Evidence Quality Tips

- Make device names visible in topology screenshots.
- Capture the command and output in the same screenshot.
- For failed pings, show multiple failed attempts.
- For ACL testing, capture `show access-lists` after generating denied traffic so hit counters are visible.
- For SSH success, show the device hostname after login.
- For SSH failure, show the attempted command and failure message.
- For port security, capture the violation count or secure MAC address table.
