# Troubleshooting Guide

## VLAN Mismatch Between Switches

Symptoms:

- PC receives the wrong IP subnet.
- Same-VLAN devices cannot communicate.
- `show vlan brief` shows access ports in the wrong VLAN.

Checks:

```text
show vlan brief
show interfaces status
show running-config interface FastEthernet0/1
```

Fix:

```text
configure terminal
interface FastEthernet0/1
switchport mode access
switchport access vlan <correct-vlan>
no shutdown
end
```

## Trunk Link Not Forming

Symptoms:

- VLANs do not pass between core and access switch.
- Management SVI on access switch cannot be reached.
- `show interfaces trunk` does not list the uplink.

Checks:

```text
show interfaces trunk
show running-config interface FastEthernet0/24
show interfaces status
```

Fix:

```text
configure terminal
interface FastEthernet0/24
switchport mode trunk
switchport trunk native vlan 999
switchport trunk allowed vlan <needed-vlans>,99,999
no shutdown
end
```

## Wrong Default Gateway Configured

Symptoms:

- PC can ping same-VLAN peers but cannot reach other VLANs.
- Server cannot be reached from other subnets.

Checks:

```text
ipconfig
show ip interface brief
```

Fix:

- User PCs in VLAN 10 should use `192.168.10.1`.
- HR PCs should use `192.168.20.1`.
- IT PCs should use `192.168.30.1`.
- Finance PCs should use `192.168.40.1`.
- Guest clients should use `192.168.50.1`.
- DMZ servers should use `192.168.60.1`.
- Management devices should use `192.168.99.1`.

## DHCP Not Assigning Addresses

Symptoms:

- PC shows `169.254.x.x` or no IP address.
- `show ip dhcp binding` is empty.

Checks:

```text
show ip dhcp binding
show running-config | section dhcp
show vlan brief
show interfaces trunk
```

Fix:

- Confirm the PC is set to DHCP.
- Confirm the access port is in the correct VLAN.
- Confirm the VLAN exists on the access switch and core switch.
- Confirm the trunk allows the VLAN.
- Confirm DHCP excluded ranges do not exclude the entire subnet.
- Confirm the SVI is up/up.

## ACL Blocking Too Much Traffic

Symptoms:

- Approved traffic fails.
- Admin to Finance ping fails.
- Guest cannot reach the allowed Web Server.

Checks:

```text
show access-lists
show running-config interface vlan <id>
show running-config | section ip access-list
```

Fix:

- Check ACL rule order. Specific permits must come before broad denies.
- Confirm the ACL is applied to the correct SVI.
- Confirm direction is inbound.
- For stateless ACLs, confirm return traffic is allowed where required.

## ACL Not Blocking Expected Traffic

Symptoms:

- Guest can ping internal VLANs.
- HR can reach Finance.
- DMZ server can ping internal PCs.

Checks:

```text
show access-lists
show running-config interface vlan <source-vlan>
```

Fix:

- Confirm the source VLAN has the correct ACL applied inbound.
- Confirm wildcard masks are correct.
- Confirm broad `permit ip any any` is not placed before deny rules.
- Generate traffic again and check ACL counters.

## SSH Not Working

Symptoms:

- SSH connection times out.
- Packet Tracer says connection refused.
- Username/password does not work.

Checks:

```text
show ip ssh
show line vty 0 4
show running-config | include username
show running-config | section line vty
```

Fix:

```text
configure terminal
ip domain-name securewave.local
username netadmin privilege 15 secret SwaveAdmin2026
crypto key generate rsa modulus 1024
ip ssh version 2
line vty 0 4
transport input ssh
login local
end
```

If AAA is enabled, use `login authentication default` only if the AAA method is correctly configured.

## RSA Key Generation Issue

Symptoms:

- `crypto key generate rsa` fails.
- SSH version does not enable.

Cause:

- Hostname or domain name may be missing.

Fix:

```text
configure terminal
hostname <device-name>
ip domain-name securewave.local
crypto key generate rsa modulus 1024
ip ssh version 2
end
```

## VTY Transport Not Set to SSH

Symptoms:

- Telnet still works.
- SSH fails or device accepts insecure management.

Check:

```text
show running-config | section line vty
show line vty 0 4
```

Fix:

```text
configure terminal
line vty 0 4
transport input ssh
login local
end
```

## AAA Login Issue

Symptoms:

- Device rejects valid credentials.
- External AAA server authentication fails.
- Local login fallback does not work.

Checks:

```text
show running-config | include aaa
show running-config | include username
show running-config | section line vty
```

Fix:

- Confirm local username exists.
- Confirm `aaa authentication login default local` is configured.
- If using external AAA, confirm the AAA server IP, key, service, and client entry.
- If Packet Tracer external AAA does not work, use local AAA fallback.

[PACKET TRACER LIMITATION]

Packet Tracer AAA support varies. In production, Cisco ISE, RADIUS, or TACACS+ would be used.

## Port Security Violation Triggered Unexpectedly

Symptoms:

- Port goes err-disabled or restricts traffic.
- A PC loses connectivity after moving devices.

Checks:

```text
show port-security
show port-security interface FastEthernet0/1
show interfaces status
```

Fix:

```text
configure terminal
interface FastEthernet0/1
shutdown
no shutdown
end
```

If sticky MAC addresses are wrong, clear them:

```text
clear port-security sticky interface FastEthernet0/1
```

If Packet Tracer does not support the clear command, remove and reapply the port-security configuration or restart the port.

## Server Not Reachable from User VLAN

Symptoms:

- Web Server page does not load.
- Ping to server fails when it should succeed.

Checks:

```text
show ip interface brief
show access-lists
show vlan brief
```

Fix:

- Confirm server static IP.
- Confirm server default gateway is `192.168.60.1`.
- Confirm server port is in VLAN 60.
- Confirm `DMZ_IN` permits service replies.
- Confirm source VLAN ACL permits the service.

## DNS Not Resolving

Symptoms:

- `http://192.168.60.20` works but `http://www.securewave.local` fails.

Checks:

- DNS Server IP is `192.168.60.10`.
- DNS service is turned on.
- A record exists for `www.securewave.local`.
- DHCP pools hand out `dns-server 192.168.60.10`.
- Client `ipconfig` shows DNS server as `192.168.60.10`.

Fix:

- Reconfigure DNS records.
- Renew DHCP by switching PC from static to DHCP again.
- Confirm ACL permits DNS to the server.

## OSPF Neighbor Not Forming

Symptoms:

- `show ip ospf neighbor` is empty.
- Internal VLANs cannot reach optional internet route.

Checks:

```text
show ip interface brief
show running-config | section router ospf
show ip ospf neighbor
show ip route
```

Fix:

- Confirm routed link IPs are in the same /30.
- Confirm interfaces are no shutdown.
- Confirm core routed port uses `no switchport`.
- Confirm both devices use OSPF Area 0.
- Confirm passive-interface is removed on the routed link.

## Wrong Cable Type Used Between Devices

Symptoms:

- Link lights remain down.
- Interfaces show down/down.

Checks:

```text
show ip interface brief
show interfaces status
```

Fix:

- Router-to-switch: copper straight-through.
- PC/server-to-switch: copper straight-through.
- Switch-to-switch: crossover or auto-MDIX.
- Router-to-router: crossover or auto-MDIX.
- Console setup: console cable.

Packet Tracer auto-connect can often choose the correct cable.

## SVI Not Coming Up

Symptoms:

- SVI shows administratively up but line protocol down.
- Inter-VLAN routing fails for that VLAN.

Checks:

```text
show ip interface brief
show vlan brief
show interfaces trunk
```

Fix:

- Ensure at least one access port or trunk carrying that VLAN is up.
- Ensure the VLAN exists.
- Ensure the SVI has `no shutdown`.
- Ensure the access switch trunk allows that VLAN.

## `no shutdown` Missing

Symptoms:

- Interface shows administratively down.

Check:

```text
show ip interface brief
show interfaces status
```

Fix:

```text
configure terminal
interface <interface-id>
no shutdown
end
```

## `ip routing` Missing on Core Switch

Symptoms:

- SVIs exist, but inter-VLAN routing does not work.
- Core switch acts only as Layer 2.

Check:

```text
show running-config | include ip routing
show ip route
```

Fix:

```text
configure terminal
ip routing
end
```

## NAT/PAT Not Working if ISP Simulation Is Included

Symptoms:

- Internal devices cannot reach optional ISP network.
- NAT translation table is empty.

Checks:

```text
show ip nat translations
show ip nat statistics
show running-config | include ip nat
show ip route
```

Fix:

- Confirm inside interface: `Edge-Router Gi0/0`.
- Confirm outside interface: `Edge-Router Gi0/1`.
- Confirm NAT ACL permits `192.168.0.0/16`.
- Confirm default route points to `203.0.113.1`.
- Confirm ISP router has a return route if testing from ISP side.

Example:

```text
interface GigabitEthernet0/0
ip nat inside
interface GigabitEthernet0/1
ip nat outside
ip nat inside source list NAT_INSIDE interface GigabitEthernet0/1 overload
```
