# SecureWave Enterprise Network — Cisco Packet Tracer

A hands-on Cisco Packet Tracer enterprise network simulation built to demonstrate VLAN segmentation, Layer 3 switching, DHCP, DMZ services, DNS resolution, web hosting, and an Edge Router connection.

This project simulates a small enterprise branch network for a fictional company called **SecureWave Solutions**. The goal was to design and validate a clean internal network where departments are separated into VLANs, users receive DHCP addresses automatically, servers are placed in a DMZ, and users can access an internal website through DNS.

> **Note:** This is a Cisco Packet Tracer simulation project. All credentials and IP addresses are lab-only and are not production secrets.

---

## Project Highlights

* Built a multi-department enterprise network topology
* Configured VLAN segmentation for Admin, HR, IT, Finance, Guest, and DMZ
* Configured a Layer 3 core switch for inter-VLAN routing
* Created DHCP pools for multiple user VLANs
* Deployed DNS and Web servers in a DMZ VLAN
* Configured DNS records for `www.securewave.local`
* Verified web access using both IP address and DNS name
* Configured an Edge Router link to the core switch
* Documented testing evidence with screenshots
* Troubleshot real configuration issues such as trunk mismatches, wrong switch ports, DHCP failures, and missing SVI IP addresses

---

## Network Topology

The network uses a Layer 3 core switch connected to multiple access switches. Each department has its own access switch and VLAN. Servers are placed behind a dedicated DMZ switch.

![Full Network Topology](Screenshots/01-full-topology.png)

---

## VLAN and IP Addressing Plan

| VLAN | Name              | Subnet          | Default Gateway | Purpose                   |
| ---- | ----------------- | --------------- | --------------- | ------------------------- |
| 10   | ADMIN             | 192.168.10.0/24 | 192.168.10.1    | Admin department users    |
| 20   | HR                | 192.168.20.0/24 | 192.168.20.1    | Human Resources users     |
| 30   | IT                | 192.168.30.0/24 | 192.168.30.1    | IT department users       |
| 40   | FINANCE           | 192.168.40.0/24 | 192.168.40.1    | Finance users             |
| 50   | GUEST             | 192.168.50.0/24 | 192.168.50.1    | Guest laptop/network      |
| 60   | DMZ_SERVERS       | 192.168.60.0/24 | 192.168.60.1    | DNS, Web, and AAA servers |
| 99   | MANAGEMENT        | 192.168.99.0/24 | 192.168.99.1    | Network management        |
| 999  | BLACK_HOLE_UNUSED | N/A             | N/A             | Unused/shutdown ports     |

---

## Server Addressing

| Server     |    IP Address | Role                                                 |
| ---------- | ------------: | ---------------------------------------------------- |
| DNS-SERVER | 192.168.60.10 | DNS resolution for internal domain                   |
| WEB-SERVER | 192.168.60.20 | Internal SecureWave website                          |
| AAA-SERVER | 192.168.60.30 | AAA server placeholder for future security expansion |

---

## Verification Evidence

### VLAN Configuration

The core switch was configured with VLANs for each department and service zone.

![VLAN Verification](Screenshots/02-core-show-vlan-brief.png)

---

### Trunk Verification

Trunk links were configured between the core switch and access switches so VLAN traffic can travel across the network.

![Trunk Verification](Screenshots/03-core-show-interfaces-trunk.png)

---

### Layer 3 Gateway Verification

The core switch provides SVI gateway interfaces for each VLAN.

![IP Interface Brief](Screenshots/04-core-show-ip-interface-brief.png)

---

### DHCP Verification

DHCP pools were configured on the core switch, and client devices received correct addresses automatically.

![DHCP Binding](Screenshots/05-core-show-ip-dhcp-binding.png)

---

### PC IP Configuration

Client devices received IP addresses from their correct VLAN subnets.

![PC IPConfig Proof](Screenshots/06-pc-ipconfig-proof.png)

---

### Admin VLAN Gateway Test

Admin client connectivity to its VLAN gateway was tested successfully.

![Admin Gateway Ping](Screenshots/07-admin-to-gateway-ping.png)

---

### DMZ Server Connectivity

Admin client connectivity to DMZ services was verified.

![Admin to DMZ Server Ping](Screenshots/08-admin-to-dmz-server-ping.png)

---

### DNS and Web Server Test

The internal website successfully opened using DNS name resolution.

![Web Server DNS Test](Screenshots/09-web-server-by-dns-name.png)

---

### Core-to-Edge Router Test

The routed link between the core switch and Edge Router was verified.

![Core Edge Ping](Screenshots/10-core-edge-ping-success.png)

---

## Troubleshooting Performed

During the build, several realistic network issues were identified and fixed:

* DHCP failed because some access switch uplink ports were configured as access ports instead of trunks.
* CDP was used to identify real physical uplink ports.
* Native VLAN mismatch messages were resolved by correcting trunk configurations.
* VLAN 60 initially lacked an SVI IP address, preventing access to DMZ servers.
* The Edge Router link initially failed because the IP address was configured on the wrong core switch interface.
* The final working design was saved after confirming VLANs, DHCP, DMZ services, DNS, web access, and Edge Router connectivity.

---

## Current Status

Implemented:

* VLAN segmentation
* Layer 3 inter-VLAN routing
* DHCP for user VLANs
* Guest VLAN
* DMZ VLAN
* DNS server
* Web server
* Internal DNS name: `www.securewave.local`
* Edge Router to Core Switch link
* Basic device password hardening

Planned next phase:

* ACL-based security rules
* HR-to-Finance blocked traffic test
* Guest-to-internal blocked traffic test
* IT-only SSH management access
* Non-IT SSH blocked test
* Final access control evidence screenshots

---

## How to Open the Project

1. Install Cisco Packet Tracer.
2. Download this repository.
3. Open the Packet Tracer file:

```text
SecureWave_Working_Base_With_Edge.pkt
```

4. Review the topology, device configurations, and testing evidence.

---

## Skills Demonstrated

* Cisco Packet Tracer network design
* VLAN segmentation
* Trunk configuration
* Layer 3 switching
* DHCP configuration
* Inter-VLAN routing
* DMZ design
* DNS and HTTP service configuration
* Network troubleshooting
* CDP-based port verification
* Technical documentation
* Evidence-based validation

---

## Lab Credential Notice

All credentials used in this project are lab-only credentials for Cisco Packet Tracer simulation. They are not real production credentials and should not be reused outside this lab.
