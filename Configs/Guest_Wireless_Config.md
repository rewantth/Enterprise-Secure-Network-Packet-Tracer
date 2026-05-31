# Guest Wireless Configuration

## Goal

Configure a Linksys WRT300N or Packet Tracer wireless access point as a guest bridge into VLAN 50. The wireless device should not act as a separate DHCP/NAT router. Guest clients should receive IP addresses from the central DHCP pool on `Core-SW`.

## Placement

| Device | Port | Connected To | VLAN | Purpose |
|---|---|---|---:|---|
| `Guest-AP` | LAN/Ethernet 1 | `DMZ-Server-SW Fa0/3` | 50 | Guest wireless bridge uplink |
| `Guest-PC1` | Wireless0 | `Guest-AP` | 50 | Guest wireless client |
| `Guest-PC2` | Wireless0 | `Guest-AP` | 50 | Guest wireless client |

## Switch Port Requirement

On `DMZ-Server-SW`, port `Fa0/3` must be configured as an access port in VLAN 50.

Relevant switch configuration:

```text
interface FastEthernet0/3
 description Guest AP LAN port in VLAN 50
 switchport mode access
 switchport access vlan 50
 switchport port-security
 switchport port-security maximum 10
 switchport port-security mac-address sticky
 switchport port-security violation restrict
 spanning-tree portfast
 spanning-tree bpduguard enable
 no shutdown
```

The MAC limit is higher on the AP uplink because several wireless client MAC addresses may appear behind this bridged access point.

## WRT300N / AP GUI Steps

1. Click the `Guest-AP` device.
2. Connect the switch cable to one of the LAN/Ethernet ports, not the Internet/WAN port.
3. Open the device GUI or Config tab.
4. Set the local IP address to:
   - IP address: `192.168.50.2`
   - Subnet mask: `255.255.255.0`
   - Default gateway: `192.168.50.1`
5. Disable DHCP server if the option is available.
6. Disable NAT/routing behavior if the option is available.
7. Configure wireless settings:
   - SSID: `SecureWave-Guest`
   - Security: WPA2-PSK if supported
   - Passphrase: `GuestAccess2026`
8. Save settings.
9. Connect `Guest-PC1` and `Guest-PC2` to `SecureWave-Guest`.
10. On each guest client, set IP configuration to DHCP.

## Guest Client Validation

On each guest client:

1. Open `Desktop > IP Configuration`.
2. Select `DHCP`.
3. Confirm the client receives:
   - IP address: `192.168.50.x`
   - Subnet mask: `255.255.255.0`
   - Default gateway: `192.168.50.1`
   - DNS server: `192.168.60.10`
4. Open `Command Prompt`.
5. Run:

```text
ipconfig
ping 192.168.50.1
ping 192.168.40.1
```

Expected results:

- Ping to `192.168.50.1` should succeed.
- Ping to Finance gateway or Finance PCs should fail because of `GUEST_IN`.

## Guest Web Test

If the DMZ Web Server is configured:

1. Open `Desktop > Web Browser` on a guest client.
2. Browse to:

```text
http://192.168.60.20
```

Expected result:

- The page should load if HTTP is enabled on the Web Server.

## Guest DNS Test

If DNS is configured:

1. Open `Desktop > Web Browser`.
2. Browse to:

```text
http://www.securewave.local
```

Expected result:

- The name should resolve to `192.168.60.20`.
- The Web Server page should load.

## Packet Tracer Limitation

[PACKET TRACER LIMITATION]

Real-world feature: Enterprise APs usually connect to a wireless LAN controller and use WPA2/WPA3 Enterprise with 802.1X, RADIUS, dynamic VLAN assignment, and centralized policy.

Why Packet Tracer may not fully support it: Packet Tracer wireless devices often simulate simplified home-router or AP behavior.

Closest Packet Tracer-compatible alternative: Use a WRT300N or AP as a bridge into VLAN 50, disable DHCP, and enforce guest restrictions with the VLAN 50 SVI ACL on the core switch.
