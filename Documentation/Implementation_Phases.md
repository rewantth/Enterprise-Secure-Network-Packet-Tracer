# Implementation Phases

| Phase | Goal | Devices Configured | Commands / Configurations Involved | Verification Command | Screenshot Evidence Required |
|---:|---|---|---|---|---|
| 1 | Build physical topology and cable devices | All devices | Place routers, switches, PCs, servers, AP, optional ISP; connect using interface mapping | Visual inspection; link lights | Full topology view |
| 2 | Configure VLANs and trunking | Core and access switches | `vlan`, `name`, `switchport mode trunk`, `switchport trunk allowed vlan`, `switchport access vlan` | `show vlan brief`, `show interfaces trunk` | VLAN table and trunk output |
| 3 | Configure SVIs and inter-VLAN routing | Core switch | `ip routing`, `interface vlan`, SVI IP addresses, `no shutdown` | `show ip interface brief`, `show ip route` | Core SVI configuration |
| 4 | Configure DHCP | Core switch and PCs | `ip dhcp excluded-address`, `ip dhcp pool`, PC DHCP GUI | `show ip dhcp binding`, PC `ipconfig` | DHCP pools and DHCP client IPs |
| 5 | Configure OSPF | Core switch and edge router | `router ospf 10`, `network`, `passive-interface`, `default-information originate` | `show ip ospf neighbor`, `show ip route` | OSPF neighbor and route table |
| 6 | Configure NAT/PAT if ISP simulation is used | Edge router | `ip nat inside`, `ip nat outside`, NAT ACL, overload | `show ip nat translations`, `show ip route` | NAT/PAT config or test result |
| 7 | Configure ACL security policies | Core switch and VTY lines on devices | Named extended ACLs, SVI `ip access-group`, VTY `access-class` | `show access-lists`, test pings/web/SSH | Named ACL config and hit counters |
| 8 | Configure SSH, AAA, passwords, and management VLAN | Routers and switches | `aaa new-model`, `username secret`, `ip ssh version 2`, RSA keys, console and VTY lines | `show ip ssh`, `show users`, `show line vty 0 4` | Successful and failed SSH tests |
| 9 | Configure port security and shut unused ports | Access switches and core unused ports | `switchport port-security`, sticky MAC, violation mode, VLAN 999, `shutdown` | `show port-security`, `show interfaces status` | Port security and unused ports |
| 10 | Perform testing and collect screenshots | All devices | Testing matrix, Simulation Mode packet flow, show commands | All required verification commands | All screenshots from checklist |

## Phase 1: Build Physical Topology and Cable All Devices

Goal: Create the Packet Tracer topology using the device names and cabling map.

Devices configured: None yet, except renaming devices.

Actions:

1. Place the edge router.
2. Place the core multilayer switch.
3. Place five access switches.
4. Place PCs, servers, guest wireless device, and optional ISP router.
5. Cable devices using `Architecture/Interface_Mapping.md`.

Verification:

```text
Check link lights
Check device labels
Confirm interfaces match mapping
```

Screenshot required: Full topology view.

## Phase 2: Configure VLANs and Trunking

Goal: Build Layer 2 segmentation.

Devices configured: `Core-SW`, `Admin-SW`, `HR-SW`, `IT-SW`, `Finance-SW`, `DMZ-Server-SW`.

Commands involved:

```text
vlan 10
name ADMIN
switchport mode trunk
switchport trunk native vlan 999
switchport trunk allowed vlan <list>
switchport mode access
switchport access vlan <id>
```

Verification:

```text
show vlan brief
show interfaces trunk
```

Screenshot required: VLAN table and trunk output.

## Phase 3: Configure SVIs and Inter-VLAN Routing

Goal: Make the core switch the default gateway for each VLAN.

Devices configured: `Core-SW`.

Commands involved:

```text
ip routing
interface vlan 10
ip address 192.168.10.1 255.255.255.0
no shutdown
```

Verification:

```text
show ip interface brief
show ip route
```

Screenshot required: Core SVI configuration.

## Phase 4: Configure DHCP

Goal: Provide IP addresses to user and guest VLANs.

Devices configured: `Core-SW`, all PCs.

Commands involved:

```text
ip dhcp excluded-address
ip dhcp pool ADMIN
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 192.168.60.10
```

Verification:

```text
show ip dhcp binding
ipconfig
```

Screenshot required: DHCP pool config and PC `ipconfig`.

## Phase 5: Configure OSPF Between Core Switch and Edge Router

Goal: Exchange routes between the branch core and edge router.

Devices configured: `Core-SW`, `Edge-Router`.

Commands involved:

```text
router ospf 10
network 10.0.0.0 0.0.0.3 area 0
passive-interface default
no passive-interface GigabitEthernet0/1
default-information originate
```

Verification:

```text
show ip ospf neighbor
show ip route
```

Screenshot required: OSPF neighbor relationship.

## Phase 6: Configure NAT/PAT if ISP Simulation Is Used

Goal: Allow internal private networks to reach an optional simulated ISP network.

Devices configured: `Edge-Router`, optional `ISP-Router`.

Commands involved:

```text
ip nat inside
ip nat outside
ip nat inside source list NAT_INSIDE interface GigabitEthernet0/1 overload
```

Verification:

```text
show ip nat translations
show ip route
```

Screenshot required: NAT config or NAT translation output if used.

## Phase 7: Configure ACL Security Policies

Goal: Enforce least privilege between VLANs.

Devices configured: `Core-SW`, VTY lines on all routers/switches.

Commands involved:

```text
ip access-list extended GUEST_IN
ip access-group GUEST_IN in
access-class SSH_IT_ONLY in
```

Verification:

```text
show access-lists
show running-config
```

Screenshot required: ACL config and hit counters.

## Phase 8: Configure SSH, AAA, Passwords, and Management VLAN

Goal: Secure administrative access.

Devices configured: All routers and switches.

Commands involved:

```text
aaa new-model
username netadmin privilege 15 secret SwaveAdmin2026
crypto key generate rsa modulus 1024
ip ssh version 2
transport input ssh
```

Verification:

```text
show ip ssh
show users
show line vty 0 4
```

Screenshot required: IT SSH success and non-IT SSH failure.

## Phase 9: Configure Port Security and Shut Unused Ports

Goal: Harden access-layer ports.

Devices configured: Access switches and unused core switch ports.

Commands involved:

```text
switchport port-security
switchport port-security maximum 2
switchport port-security mac-address sticky
switchport port-security violation restrict
shutdown
```

Verification:

```text
show port-security
show port-security interface FastEthernet0/1
show interfaces status
```

Screenshot required: Port security and disabled unused ports.

## Phase 10: Perform Testing and Collect Screenshots

Goal: Prove that the design works as documented.

Devices configured: No new configuration unless troubleshooting is needed.

Actions:

1. Run all allowed communication tests.
2. Run all blocked communication tests.
3. Run SSH tests.
4. Run DHCP, OSPF, trunk, route, and ACL verification.
5. Capture all screenshots listed in the screenshot checklist.

Verification:

```text
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
show ip ospf neighbor
show access-lists
show ip dhcp binding
show port-security
```

Screenshot required: All evidence screenshots.
