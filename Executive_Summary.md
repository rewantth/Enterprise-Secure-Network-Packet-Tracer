# Executive Summary

SecureWave Solutions requires a secure branch office network that separates business departments, protects sensitive systems, provides controlled access to DMZ services, and limits network-device administration to authorized IT users.

This Cisco Packet Tracer project simulates a realistic enterprise branch deployment using a layered network design. A multilayer core switch performs inter-VLAN routing through switched virtual interfaces, while an edge router provides WAN connectivity, OSPF default route advertisement, and optional NAT/PAT for simulated internet access. Layer 2 access switches connect departments to the core using trunk links, and each department is placed in its own VLAN.

The design uses VLAN segmentation, named extended ACLs, DMZ isolation, guest wireless restrictions, management VLAN separation, SSHv2 hardening, local AAA fallback, and port security. Finance and Admin systems are protected from unauthorized VLANs, Guest traffic is isolated from internal networks, DMZ servers expose only approved DNS and web services, and only IT users are allowed to manage network devices over SSH.

This project demonstrates hands-on skills aligned with junior network engineer, network administrator, security analyst, and network security engineer responsibilities. It includes a complete IP plan, VLAN plan, topology explanation, interface mapping, Cisco IOS-style configuration files, testing matrix, troubleshooting guide, evidence checklist, and presentation script.

Packet Tracer does not fully reproduce every production-grade enterprise feature. Where appropriate, this project clearly marks Packet Tracer limitations and provides the closest compatible alternative. In a production environment, this design would be strengthened with Cisco ISE, 802.1X, a dedicated firewall such as Cisco ASA or Firepower, redundant links, EtherChannel, HSRP, centralized logging, NetFlow, SIEM integration, vulnerability scanning, and automated configuration backups.

The outcome is a portfolio-ready enterprise security simulation that shows practical understanding of segmentation, least-privilege access, routing, secure administration, Layer 2 security, DMZ design, guest isolation, and professional network documentation.
