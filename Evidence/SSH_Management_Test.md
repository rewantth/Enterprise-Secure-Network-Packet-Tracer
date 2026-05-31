# SSH Management Test

## Goal

Validate that only IT users can manage network devices over SSH and that non-IT users are blocked.

## Device Management Targets

| Device | Management IP | Expected SSH Source |
|---|---:|---|
| `Core-SW` | 192.168.99.1 | IT VLAN only |
| `Admin-SW` | 192.168.99.11 | IT VLAN only |
| `HR-SW` | 192.168.99.12 | IT VLAN only |
| `IT-SW` | 192.168.99.13 | IT VLAN only |
| `Finance-SW` | 192.168.99.14 | IT VLAN only |
| `DMZ-Server-SW` | 192.168.99.15 | IT VLAN only |
| `Edge-Router` | 192.168.99.254 | IT VLAN only |

## IT SSH Success Test

Source: `IT-PC1`, expected DHCP address `192.168.30.21`.

Commands:

```text
ssh -l netadmin 192.168.99.1
ssh -l netadmin 192.168.99.254
```

Expected result:

- SSH session opens.
- Login banner appears.
- Username/password is accepted.
- Device prompt appears, such as `Core-SW>` or `Edge-Router>`.

Screenshot placeholder:

```text
Screenshots/15_it_ssh_success.png
```

Professional explanation:

IT users are allowed to manage infrastructure devices because the IT VLAN is the approved administrative source. SSHv2 protects credentials in transit, and VTY access-class restrictions limit remote access to the IT subnet.

## Non-IT SSH Blocked Test

Source: `HR-PC1`, expected DHCP address `192.168.20.21`.

Commands:

```text
ssh -l netadmin 192.168.99.1
ssh -l netadmin 192.168.99.254
```

Expected result:

- SSH fails, times out, or is refused.
- HR should not reach Management VLAN resources.

Screenshot placeholder:

```text
Screenshots/16_non_it_ssh_blocked.png
```

Professional explanation:

Non-IT users are blocked from device administration by a combination of source VLAN ACLs and VTY access-class restrictions. This protects the management plane from unauthorized access attempts.

## `show ip ssh` Verification

Run on router and switches:

```text
show ip ssh
```

Expected result:

- SSH is enabled.
- SSH version 2 is active.

Screenshot placeholder:

```text
Screenshots/show_ip_ssh.png
```

## `show users` Verification

Run during an active IT SSH session:

```text
show users
```

Expected result:

- Shows active VTY session from an IT VLAN IP.

Screenshot placeholder:

```text
Screenshots/show_users_it_session.png
```

## VTY Configuration Verification

Run:

```text
show running-config | section line vty
show line vty 0 4
```

Expected configuration:

```text
line vty 0 4
 access-class SSH_IT_ONLY in
 login authentication default
 transport input ssh
```

If Packet Tracer rejects `login authentication default`, use:

```text
login local
```

Screenshot placeholder:

```text
Screenshots/show_line_vty_0_4.png
```

## Related Controls

- SSH version 2
- Local username with `secret`
- `enable secret`
- `service password-encryption`
- `aaa new-model`
- Local AAA fallback
- VTY `access-class SSH_IT_ONLY in`
- `transport input ssh`
- Telnet disabled
- Management VLAN 99

## Packet Tracer Limitation

[PACKET TRACER LIMITATION]

Some Packet Tracer device images may not support every AAA command exactly. If external AAA or `login authentication default` fails, use `login local` with the local user database. In a real enterprise environment, centralized authentication would usually be provided by Cisco ISE, RADIUS, or TACACS+.
