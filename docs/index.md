# SecureWave Enterprise Network — Project Evidence Page

**Cisco Packet Tracer · Enterprise Branch Network Simulation**

📥 [Download Packet Tracer File](SecureWave_Working_Base_Final.pkt) &nbsp;|&nbsp; 🐙 [GitHub Repository](https://github.com/rewantth/Enterprise-Secure-Network-Packet-Tracer)

> This project simulates a secure enterprise branch network for SecureWave Solutions.  
> Built in Cisco Packet Tracer — not a production environment.  
> All credentials shown are lab-only simulation credentials.

---

## Project Summary

A multi-VLAN (8 VLANs) enterprise branch network featuring Layer 3 inter-VLAN routing, DHCP, a DMZ with DNS and web services, guest segmentation, and an edge router routed uplink. Built and documented as a networking/cybersecurity portfolio project.

---

## Verified Working Features

- ✅ VLANs 10, 20, 30, 40, 50, 60, 99, 999 created and active
- ✅ Trunk links between CORE-SW and all access switches
- ✅ Inter-VLAN routing via Layer 3 SVIs on CORE-SW
- ✅ DHCP pools — all user VLANs receiving correct IPs
- ✅ DMZ servers reachable (DNS: 192.168.60.10 / Web: 192.168.60.20)
- ✅ `www.securewave.local` resolves and loads in browser
- ✅ Edge Router ↔ Core Switch routed link (10.0.0.1/30 ↔ 10.0.0.2/30)
- ✅ Guest VLAN 50 segmented and working
- ✅ Black Hole VLAN 999 assigned to unused ports

---

## Screenshot Evidence

### 1. Full Topology
![Full Topology](Screenshots/01-full-topology.png)

---

### 2. VLAN Table — `show vlan brief`
![VLAN Brief](Screenshots/02-core-show-vlan-brief.png)

---

### 3. Trunk Links — `show interfaces trunk`
![Trunk Links](Screenshots/03-core-show-interfaces-trunk.png)

---

### 4. SVI and Interface Status — `show ip interface brief`
![IP Interface Brief](Screenshots/04-core-show-ip-interface-brief.png)

---

### 5. DHCP Bindings — `show ip dhcp binding`
![DHCP Bindings](Screenshots/05-core-show-ip-dhcp-binding.png)

---

### 6. PC IP Confirmation — `ipconfig`
![PC ipconfig](Screenshots/06-pc-ipconfig-proof.png)

---

### 7. Admin PC to Gateway Ping
![Admin Gateway Ping](Screenshots/07-admin-to-gateway-ping.png)

---

### 8. Admin PC to DMZ Server Ping
![Admin to DMZ Ping](Screenshots/08-admin-to-dmz-server-ping.png)

---

### 9. DNS Name Resolution — `www.securewave.local`
![DNS Browser](Screenshots/09-web-server-by-dns-name.png)

---

### 10. Edge Router to Core Switch Ping
![Edge to Core Ping](Screenshots/10-core-edge-ping-success.png)

---

## VLAN & IP Reference

| VLAN | Name | Subnet | Gateway |
|---|---|---|---|
| 10 | ADMIN | 192.168.10.0/24 | 192.168.10.1 |
| 20 | HR | 192.168.20.0/24 | 192.168.20.1 |
| 30 | IT | 192.168.30.0/24 | 192.168.30.1 |
| 40 | FINANCE | 192.168.40.0/24 | 192.168.40.1 |
| 50 | GUEST | 192.168.50.0/24 | 192.168.50.1 |
| 60 | DMZ_SERVERS | 192.168.60.0/24 | 192.168.60.1 |
| 99 | MANAGEMENT | 192.168.99.0/24 | 192.168.99.1 |
| 999 | BLACK_HOLE | unused | — |

---

## Planned — Phase 2 Security

The following security controls are planned and will be added in Phase 2:

- Named extended ACLs (HR blocked from Finance, Guest blocked from internal VLANs)
- SSH v2 restricted to IT VLAN only via VTY access-class
- Port security with sticky MAC on all access ports
- OSPF Area 0 between Edge Router and Core Switch

---

## Troubleshooting Log

| Issue | Cause | Fix |
|---|---|---|
| DHCP not working on Admin/HR/IT/Finance | Access switch uplink ports set as access, not trunk | Used CDP to identify real uplink port, set as trunk |
| DMZ servers unreachable | VLAN 60 SVI had no IP | Added `ip address 192.168.60.1 255.255.255.0` to `interface vlan 60` |
| Edge Router pings failing | Core routed IP on wrong interface | Moved `10.0.0.2/30` to correct port `Fa0/1` confirmed by CDP |

---

## Lab Credential Notice

This Packet Tracer simulation includes lab-only device credentials for local testing. No real production credentials are used.

---

*SecureWave Enterprise Network · Cisco Packet Tracer Simulation · [@rewantth](https://github.com/rewantth)*
