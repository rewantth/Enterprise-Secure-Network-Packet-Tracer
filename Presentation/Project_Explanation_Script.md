# Project Explanation Script

## 3-Minute Professional Presentation Script

Hello, my project is called Enterprise Secure Branch Network with Zero-Trust-Inspired Segmentation, DMZ Isolation, ACL Enforcement, SSH Hardening, AAA, Port Security, and Guest Network Controls.

I built this as a Cisco Packet Tracer simulation for a fictional company called SecureWave Solutions. The goal was to design a realistic secure branch office network, not just connect a few devices. I wanted the project to show how a junior network or security engineer might help build and validate a segmented enterprise branch environment.

The topology uses one edge router, one multilayer core switch, five Layer 2 access switches, a guest wireless segment, a DMZ server network, and a dedicated management network. The core switch performs inter-VLAN routing using SVIs, which is more realistic for this type of branch design than router-on-a-stick. The edge router handles WAN routing, optional NAT/PAT, and default route advertisement into OSPF.

Each department is separated into its own VLAN. Admin uses VLAN 10, HR uses VLAN 20, IT uses VLAN 30, Finance uses VLAN 40, Guest uses VLAN 50, DMZ servers use VLAN 60, and device management uses VLAN 99. Unused switch ports are shut down and assigned to VLAN 999, which acts as a black-hole VLAN.

The security design focuses on least privilege. Admin is allowed to access Finance and other business networks. IT is allowed to manage devices using SSH. HR is blocked from Finance. Guest users are blocked from all internal networks and can access only approved DMZ DNS and Web services. DMZ servers are prevented from initiating traffic into internal VLANs unless explicitly allowed.

I used named extended ACLs on the core switch and applied them inbound on the source VLAN SVIs. This keeps enforcement close to the source and makes the policy easier to document. I also configured SSH version 2, local AAA fallback, encrypted credentials, legal login banners, console and VTY protection, and VTY access restrictions so only IT users can manage infrastructure devices.

For access-layer security, I configured port security on user-facing ports, limited the number of MAC addresses, used sticky MAC learning, and shut down unused ports. The guest wireless router is configured as an access point or bridge into VLAN 50, with DHCP disabled so clients receive addresses from the central DHCP service on the core switch.

The project includes a full IP plan, VLAN plan, interface mapping, cable table, CLI configurations, server setup steps, testing checklist, troubleshooting guide, and evidence checklist. The validation section includes attack and defense scenarios such as Guest trying to reach Finance, HR trying to reach Finance, non-IT SSH attempts, IT SSH success, port-security violations, and DMZ-to-internal traffic attempts.

The main lesson from this project is that segmentation by itself is not enough. VLANs create separation, but ACLs, management-plane hardening, secure routing, port security, and testing evidence turn the design into an enforceable security architecture.

## 45-Second Interview Answer

I built a Cisco Packet Tracer enterprise branch security simulation for a fictional company called SecureWave Solutions. The design uses a Layer 3 core switch with SVIs for inter-VLAN routing, an edge router for WAN routing and optional NAT/PAT, OSPF between the core and edge, and separate VLANs for Admin, HR, IT, Finance, Guest, DMZ servers, and Management.

The security focus was least privilege. I used named extended ACLs to block Guest from internal networks, prevent HR from reaching Finance, restrict DMZ servers from initiating into internal VLANs, and allow only IT to manage devices through SSH. I also configured SSHv2, local AAA fallback, port security, disabled unused ports, and VLAN 999 for black-hole unused ports. The project includes full documentation, configs, testing evidence, and troubleshooting steps.

## Prepared Answer: What Would You Improve in a Real Production Network?

If this were a real production network, I would add several improvements beyond what Packet Tracer can fully simulate.

First, I would use Cisco ISE with RADIUS or TACACS+ for centralized identity-based access control instead of relying mainly on local AAA fallback. I would also add 802.1X port-based authentication so users and devices must authenticate before receiving network access.

Second, I would place a real firewall such as Cisco ASA or Cisco Firepower between internal, DMZ, guest, and internet zones. IOS ACLs are useful for this simulation, but a production firewall would provide stateful inspection, better logging, application visibility, and stronger zone-based policy enforcement.

Third, I would improve availability with redundant links, EtherChannel, redundant core or distribution switches, and HSRP for default gateway redundancy. That would reduce single points of failure.

Fourth, I would add centralized logging and monitoring, including syslog, SIEM integration, NetFlow or telemetry, alerting, and configuration backup. I would also include vulnerability scanning for DMZ services and a documented change-management process.

Those additions would make the design more resilient, observable, and production-ready while keeping the same segmentation and least-privilege principles.

## Short LinkedIn Explanation

I completed a Cisco Packet Tracer enterprise branch security simulation for SecureWave Solutions. The project includes VLAN segmentation, Layer 3 SVIs, OSPF, DHCP, DMZ isolation, guest wireless controls, named ACL enforcement, SSH hardening, AAA fallback, port security, and a complete validation checklist.

The focus was building a portfolio-ready simulation that demonstrates practical network/security engineering skills: secure topology design, least-privilege access control, management-plane hardening, and evidence-based testing.

## Resume Bullet Section

- Designed a Cisco Packet Tracer enterprise branch security simulation using VLAN segmentation, Layer 3 SVIs, OSPF, DHCP, DMZ isolation, and guest wireless controls.
- Implemented least-privilege traffic policies with named extended ACLs to restrict Guest, HR, Finance, DMZ, and Management VLAN access.
- Hardened Cisco device administration with SSHv2, local AAA fallback, encrypted credentials, legal login banners, and VTY source restrictions.
- Configured access-layer protections including port security, sticky MAC learning, violation mode restrict, disabled unused ports, and VLAN 999 black-hole assignment.
- Created professional technical documentation including topology diagrams, interface mapping, IP plan, security policy matrix, testing checklist, troubleshooting guide, and evidence plan.

## Presentation Q&A Prep

### Why did you use a Layer 3 switch instead of router-on-a-stick?

I used a Layer 3 core switch because it better represents an enterprise branch design. SVIs let the core switch route between VLANs locally, while the edge router focuses on WAN, NAT/PAT, and default route advertisement. Router-on-a-stick works for small labs, but it is less scalable because all inter-VLAN traffic must traverse the router interface.

### Why did you place ACLs inbound on source VLANs?

Applying ACLs inbound on source VLAN SVIs blocks unauthorized traffic close to where it enters the routed network. That reduces unnecessary traffic traversal and makes the policy easier to map to source VLAN behavior.

### Why does the DMZ ACL allow some return traffic?

Cisco IOS ACLs are stateless. They do not automatically track sessions like a firewall. To allow approved DNS and web requests to work, the DMZ ACL permits specific service replies while still blocking DMZ servers from initiating new traffic into internal VLANs.

### Why is Guest allowed to reach the DMZ Web Server?

The policy allows Guest users to access only approved DMZ services. This simulates a controlled guest portal or public web service while still blocking Guest from Admin, HR, IT, Finance, and Management networks.

### Why use VLAN 999?

VLAN 999 is used for unused ports and as the trunk native VLAN. It has no gateway and no production devices. This helps prevent unused or untagged traffic from landing in a real user VLAN.
