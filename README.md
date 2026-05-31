# SecureWave Enterprise Network — Cisco Packet Tracer

A hands-on enterprise network simulation built in **Cisco Packet Tracer**, demonstrating VLAN segmentation, Layer 3 switching, DHCP, DMZ services, DNS resolution, web hosting, and Edge Router connectivity.

🌐 **Live Documentation:**
https://rewantth.github.io/Enterprise-Secure-Network-Packet-Tracer/

📦 **Packet Tracer File:**
[`SecureWave_Working_Base_With_Edge.pkt`](SecureWave_Working_Base_With_Edge.pkt)

> This project is a Cisco Packet Tracer simulation. The `.pkt` file must be opened using Cisco Packet Tracer.

---

## 🔎 Project Overview

This project simulates a secure enterprise branch network for a fictional company, **SecureWave Solutions**.

The network includes separate VLANs for Admin, HR, IT, Finance, Guest users, and DMZ servers. A Layer 3 core switch handles inter-VLAN routing and DHCP services. DNS and Web servers are hosted in the DMZ, and users can access the internal website through `www.securewave.local`.

---

## 🧠 What I Built

* Department-based VLAN segmentation
* Layer 3 core switch with SVI gateways
* DHCP pools for multiple VLANs
* Trunk links between core and access switches
* DMZ network for DNS, Web, and AAA servers
* Internal DNS resolution for `www.securewave.local`
* Edge Router to Core Switch routed link
* Basic device password hardening
* Testing evidence through screenshots

---

## 🖥️ Network Topology

![Full Topology](Screenshots/01-full-topology.png)

---

## 🧩 VLAN Design

| VLAN | Name              | Subnet          | Gateway      | Purpose                   |
| ---- | ----------------- | --------------- | ------------ | ------------------------- |
| 10   | ADMIN             | 192.168.10.0/24 | 192.168.10.1 | Admin users               |
| 20   | HR                | 192.168.20.0/24 | 192.168.20.1 | HR users                  |
| 30   | IT                | 192.168.30.0/24 | 192.168.30.1 | IT users                  |
| 40   | FINANCE           | 192.168.40.0/24 | 192.168.40.1 | Finance users             |
| 50   | GUEST             | 192.168.50.0/24 | 192.168.50.1 | Guest laptop/network      |
| 60   | DMZ_SERVERS       | 192.168.60.0/24 | 192.168.60.1 | DNS, Web, and AAA servers |
| 99   | MANAGEMENT        | 192.168.99.0/24 | 192.168.99.1 | Management VLAN           |
| 999  | BLACK_HOLE_UNUSED | N/A             | N/A          | Unused ports              |

---

## 🛠️ Services Configured

| Service             | Device     | IP Address    |
| ------------------- | ---------- | ------------- |
| DNS                 | DNS-SERVER | 192.168.60.10 |
| Web Server          | WEB-SERVER | 192.168.60.20 |
| AAA Placeholder     | AAA-SERVER | 192.168.60.30 |
| Edge Router         | EDGE-R1    | 10.0.0.1      |
| Core Edge Interface | CORE-SW    | 10.0.0.2      |

---

## ✅ Verification Evidence

### VLAN Verification

![VLAN Brief](Screenshots/02-core-show-vlan-brief.png)

### Trunk Verification

![Trunk Verification](Screenshots/03-core-show-interfaces-trunk.png)

### Layer 3 Gateway Verification

![IP Interface Brief](Screenshots/04-core-show-ip-interface-brief.png)

### DHCP Verification

![DHCP Binding](Screenshots/05-core-show-ip-dhcp-binding.png)

### PC IP Configuration

![PC IPConfig Proof](Screenshots/06-pc-ipconfig-proof.png)

### Admin Gateway Test

![Admin Gateway Ping](Screenshots/07-admin-to-gateway-ping.png)

### DMZ Server Connectivity

![Admin to DMZ Ping](Screenshots/08-admin-to-dmz-server-ping.png)

### DNS-Based Web Access

![Web Server DNS](Screenshots/09-web-server-by-dns-name.png)

### Core-to-Edge Connectivity

![Core Edge Ping](Screenshots/10-core-edge-ping-success.png)

---

## 🧪 Troubleshooting Highlights

During the build, I fixed several realistic network issues:

* DHCP failures caused by access switch uplink ports being configured as access ports instead of trunk ports.
* Native VLAN mismatch warnings caused by incorrect trunk/access port roles.
* DMZ server access failure caused by a missing VLAN 60 SVI IP address.
* Edge Router ping failure caused by assigning the routed IP to the wrong core switch interface.
* Port identification issues resolved using `show cdp neighbors`.

These troubleshooting steps helped validate the network the way a real network engineer would: by checking physical links, VLAN membership, trunks, DHCP bindings, SVIs, and routing paths.

---

## 🚀 Current Status

Implemented:

* VLAN segmentation
* Trunking
* DHCP
* Inter-VLAN routing
* DMZ DNS/Web services
* Web access using `www.securewave.local`
* Edge Router to Core Switch link
* Basic password hardening
* Screenshot-based validation

Planned next phase:

* ACL-based traffic filtering
* Guest-to-internal blocking
* HR-to-Finance blocking
* IT-only SSH management access
* Final security validation screenshots

---

## 📂 How to Use

1. Download the repository.
2. Open `SecureWave_Working_Base_With_Edge.pkt` in Cisco Packet Tracer.
3. Review the topology, VLANs, DHCP settings, DNS/Web services, and test evidence.
4. Use the screenshots in this repository to verify the working configuration.

---

## 🔐 Lab Credential Notice

All credentials used in this project are lab-only credentials for Cisco Packet Tracer simulation. They are not real production credentials and should not be reused outside this lab.

---

