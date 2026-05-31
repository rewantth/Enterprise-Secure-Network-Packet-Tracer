# Pending Security Tests

This file tracks security evidence that still needs to be captured before the repository is fully GitHub-ready.

## Pending Evidence Table

| Test | Source | Target | Expected Result | Screenshot Needed | Status |
|---|---|---|---|---|---|
| Guest blocked from Admin | `Guest-PC1` | `Admin-PC1` | Ping fails | `Screenshots/13_guest_to_internal_blocked.png` | Pending |
| Guest blocked from Finance | `Guest-PC1` | `Finance-PC1` | Ping fails | `Screenshots/guest_to_finance_blocked.png` | Pending |
| Guest allowed to Web Server | `Guest-PC1` | `Web-Server` | HTTP succeeds | `Screenshots/14_guest_to_web_success.png` | Pending |
| HR blocked from Finance | `HR-PC1` | `Finance-PC1` | Ping fails | `Screenshots/12_hr_to_finance_blocked.png` | Pending |
| IT SSH allowed | `IT-PC1` | `Core-SW` / `Edge-Router` | SSH succeeds | `Screenshots/15_it_ssh_success.png` | Pending |
| Non-IT SSH blocked | `HR-PC1` or `Admin-PC1` | `Core-SW` / `Edge-Router` | SSH fails | `Screenshots/16_non_it_ssh_blocked.png` | Pending |
| DMZ initiation blocked | `DNS-Server` or `Web-Server` | Internal VLAN PC | Ping fails | `Screenshots/dmz_to_internal_blocked.png` | Pending |
| Port security violation | Unauthorized endpoint | Protected access port | Violation count increases or traffic is restricted | `Screenshots/25_show_port_security_interface.png` | Pending |
| ACL counters | `Core-SW` | ACL table | Deny counters increment after blocked tests | `Screenshots/08_show_access_lists.png` | Pending |

## Notes

- Do not mark a test complete unless the screenshot exists in `Screenshots/`.
- Capture the source device, command, and result in the same screenshot where possible.
- For blocked tests, capture `show access-lists` after the attempt to show ACL hit counters.
- If Packet Tracer does not support an SSH or AAA behavior exactly, document the limitation instead of overstating the result.
