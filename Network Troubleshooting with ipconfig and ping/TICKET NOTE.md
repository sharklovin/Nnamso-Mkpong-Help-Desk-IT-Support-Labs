# Help Desk Ticket #0054

**Category:** Network Connectivity - Internet Down Report
**Priority:** Standard
**Status:** Resolved

---

## Ticket Details

| Field | Value |
|------|------|
| Ticket ID | 0054 |
| Date Opened | March 2026 |
| Date Resolved | March 2026 |
| Assigned To | Nnamso Mkpong - IT Support |
| Machine | WIN11_CLIENT01 |
| Client IP | [CLIENT_IP] |
| Default Gateway | [DEFAULT_IP] |
| DNS Server | [DC_IP] |
| Domain | mylab.local |

---

## Issue Reported

User on **WIN11_CLIENT01** reported that the internet was not working and they were unable to browse websites or reach external services.

No hardware changes had been made prior to the report. The fault was investigated using a structured command line diagnostic sequence before any changes were attempted.

---

## Investigation

**Baseline check — before fault simulation:**

1. Ran `ipconfig /all` and recorded full network configuration.
   - IP Address: [CLIENT_IP] (DHCP lease active)
   - Default Gateway: [DEFAULT_IP]
   - DNS Server: [DC_IP] (Domain Controller)
   - DHCP Enabled: Yes

2. `ping 127.0.0.1` — Pass (local TCP/IP stack healthy)
3. `ping [CLIENT_IP]` — Pass (NIC active and IP correctly bound)
4. `ping [DEFAULT_GATEWAY]` — Pass (LAN segment and gateway reachable)
5. `ping 8.8.8.8` — Pass (internet path fully working)

Baseline confirmed: all four network layers functional before fault was introduced.

**Fault simulation:**

6. Virtual network adapter disconnected to simulate reported failure.
7. `ping 8.8.8.8` - **FAIL: General failure** (no route available, adapter down)
8. `ipconfig /release` - **FAIL: No operation can be performed on Ethernet0 while it has its media disconnected**
9. `ipconfig /renew` - **FAIL: No operation can be performed on Ethernet0 while it has its media disconnected**
10. `arp -a` — **No ARP Entries Found** (gateway MAC not cached, no LAN communication)

**Root Cause Identified:**
The network adapter was disconnected. The fault existed at the physical/adapter layer. The TCP/IP stack, IP configuration, DHCP server, DNS server, and internet path were all confirmed healthy. No software or configuration fault was present.

---

## Actions Taken

1. Ran `ipconfig /all` to capture baseline network configuration.
2. Ran layered ping sequence to confirm all layers working at baseline.
3. Disconnected virtual network adapter to reproduce the reported failure.
4. Ran `ping 8.8.8.8` - confirmed General failure during fault state.
5. Ran `ipconfig /release` and `ipconfig /renew` — confirmed media disconnected error.
6. Ran `arp -a` - confirmed empty ARP cache consistent with no adapter link.
7. Reconnected the network adapter.
8. DHCP lease automatically re-established on reconnection.
9. Ran `ping 8.8.8.8` - confirmed 4/4 replies, connectivity fully restored.

---

## Outcome

Internet connectivity confirmed restored on **WIN11_CLIENT01** after reconnecting the network adapter.

No changes were required to:

- IP configuration
- DNS settings
- DHCP server
- Gateway or routing

Root cause was confirmed as a **disconnected network adapter** at the physical layer.

---

## Validation Evidence

- `ipconfig /all` baseline recorded before fault
- All four ping layers passed at baseline
- `ping 8.8.8.8` returned General failure during fault state
- `ipconfig /release` and `/renew` returned media disconnected error
- `arp -a` returned no entries during fault state
- `ping 8.8.8.8` returned 4/4 replies after adapter reconnected

---

## Troubleshooting Logic

| Check | Command | Result |
|------|------|------|
| TCP/IP stack | `ping 127.0.0.1` | Pass |
| NIC and IP binding | `ping [CLIENT_IP]` | Pass |
| LAN and gateway | `ping [DEFAULT_GATEWAY]` | Pass |
| Internet path (baseline) | `ping 8.8.8.8` | Pass |
| Internet path (fault) | `ping 8.8.8.8` | Failed — General failure |
| DHCP release | `ipconfig /release` | Failed — Media disconnected |
| DHCP renew | `ipconfig /renew` | Failed — Media disconnected |
| ARP cache | `arp -a` | Failed - No entries |
| Internet path (restored) | `ping 8.8.8.8` | Pass |
| Root cause | Network adapter | Disconnected |
| Resolution | Reconnect adapter | Access restored |

---

## Real World Relevance

Internet connectivity complaints are among the most frequent helpdesk calls in any organisation. This lab demonstrates:

- Structured network diagnostic methodology
- Layered ping sequence from loopback to internet
- Reading and interpreting ipconfig /all output
- Understanding the difference between General failure and Request timed out
- Using arp -a as a supporting diagnostic tool
- Distinguishing adapter-layer faults from software and configuration faults
- Real world help desk troubleshooting workflow

---

## Ticket Closure Note

Internet connectivity restored for affected user on WIN11_CLIENT01. Root cause identified as a disconnected network adapter at the physical layer. Issue resolved without any configuration changes. Diagnostic sequence completed in under five minutes using ipconfig and ping.

**Documented by:**
Nnamso Mkpong
IT Support Portfolio
