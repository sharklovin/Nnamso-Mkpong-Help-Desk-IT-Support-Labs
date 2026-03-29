# Network Troubleshooting with ipconfig and ping

> **Author:** Nnamso Mkpong
>
> **Domain:** Network Diagnostics - Command Line Tools
>
> **Environment:** Windows 11 Client (Virtualised)
> 
> **Completed:** March 2026

---

## Objective

Use core Windows command line tools to diagnose a simulated internet connectivity failure, identify exactly where in the network stack the fault occurs, restore connectivity and document the diagnostic sequence in the order it was tested.

---

## Business Scenario

> **Ticket #0054 | User Reports Internet Down - Connectivity Investigation**
>
> A user calls the helpdesk to report that the internet is not working on their machine. Before escalating or replacing hardware, IT support runs a structured command line diagnostic to determine whether the fault is in the local TCP/IP stack, the network adapter, the LAN, the gateway or the path to the internet. The network adapter is then deliberately disconnected to simulate the failure and the diagnostic sequence is repeated to capture the difference between a working and a broken state.

This scenario trains the most important first-response skill in network support: using a repeatable, layered ping sequence to isolate exactly where connectivity breaks, rather than guessing or escalating immediately.

---

## Environment and Tools Used

| Component | Detail |
|---|---|
| **Client OS** | Windows 11 |
| **Hostname** | WIN11_CLIENT01 |
| **Domain** | [DOMAIN_NAME] |
| **Client IP** | [CLIENT_IP] (DHCP lease) |
| **Subnet Mask** | 255.255.255.0 |
| **Default Gateway** | [DEFAULT_GATEWAY]|
| **DNS Server** | [DC_IP] (Domain Controller) |
| **DHCP Server** | [DC_IP] |
| **Public IP tested** | 8.8.8.8 (Google DNS) |
| **Tools Used** | Command Prompt, ipconfig, ping, arp |

---

## Steps Performed

---

### Phase 1 - Capture the Baseline Configuration

**Step 1.1 - Run ipconfig /all and Record the Network Profile**

Open **Command Prompt** and run:

```cmd
ipconfig /all
```

This returns the full network configuration including IP address, MAC address, subnet, gateway, DNS and DHCP lease details. Record these values before any changes are made as they are the baseline you compare against after a fault is introduced.

<img width="1018" height="624" alt="Run ipconfig all and Record the Network Profile" src="https://github.com/user-attachments/assets/7ba23cd9-831f-42f6-9da6-ae4b8897c164" />



Key values confirmed from this output:

| Field | Value | Significance |
|---|---|---|
| Host Name | WIN11_CLIENT01 | Machine identity confirmed |
| Primary DNS Suffix |[DOMAIN NAME] | Domain membership confirmed |
| Physical Address | [MAC_ADDRESS] | MAC address of the NIC |
| DHCP Enabled | Yes | IP assigned dynamically |
| IPv4 Address | [CLIENT_IP] | Client's current IP |
| Default Gateway | [DEFAULT_GATEWAY] | Router / exit point for all traffic |
| DHCP Server |  [DHCP_IP] | Router is also providing DHCP leases |
| DNS Servers | [DC_IP] | Domain Controller handles DNS |

> Recording ipconfig /all at the start of every network fault investigation is non-negotiable. Without the baseline, you cannot prove what changed or confirm what was restored. It also tells you immediately whether the machine has an IP at all.
---

### Phase 2 - Run the Layered Ping Sequence (Normal Connectivity)

**Step 2.1 - Ping the Loopback Address (Layer 1 - TCP/IP Stack)**

```cmd
ping 127.0.0.1
```
<img width="894" height="259" alt="Ping the Loopback Address" src="https://github.com/user-attachments/assets/42fd3967-d0cf-4341-88e0-b65604a41ff1" />



>  4 out of 4 packets returned with sub-1ms response times. The local TCP/IP stack is functioning correctly.  A failure here would indicate a corrupted TCP/IP stack or disabled networking service.

---

**Step 2.2 - Ping the Machine's Own IP Address (Layer 2 - NIC and IP Binding)**

```cmd
ping [CLIENT_IP]
```

<img width="830" height="253" alt="pinging my ip" src="https://github.com/user-attachments/assets/0289c006-bb42-4b43-be53-a0d244c87297" />

>  4 out of 4 packets returned with sub-1ms response times. The network adapter is active, the IP address is correctly bound to it, and the machine is responding to traffic addressed to its own IP. A failure here with a passing loopback test would indicate the adapter is down or the IP is incorrectly assigned.

---

**Step 2.3 - Ping the Default Gateway (Layer 3 - LAN Connectivity)**

```cmd
ping [DEFAULT_GATEWAY]
```

<img width="874" height="250" alt="Ping the Default Gateway " src="https://github.com/user-attachments/assets/ff03eab0-5a56-4807-adc0-103e2befcd84" />



> 4 out of 4 packets returned. Response times of 1–3ms are consistent with a local LAN hop. The machine can reach the router, which means the cable, switch, and LAN segment are all functioning. A failure here with passing loopback and own-IP tests would point to a cable fault, switch port issue or gateway configuration problem.

---

**Step 2.4 - Ping a Public IP Address (Layer 4 - Internet Path)**

```cmd
ping 8.8.8.8
```

<img width="813" height="247" alt="ping internet" src="https://github.com/user-attachments/assets/520655b5-91a3-40c6-adcc-f3cadff59fa4" />


>  4 out of 4 packets returned. Response times of 31–43ms are normal for a public internet target. Packets are leaving the local network, routing through the internet, reaching Google's DNS servers and returning successfully. Full connectivity confirmed at baseline.

---

### Phase 3 - Simulate the Fault (Disconnect the Network Adapter)

**Step 3.1 - Disconnect the VM Network Adapter**

The virtual machine's network adapter is disconnected to simulate the user's reported internet failure. This is equivalent to unplugging a network cable or disabling the adapter on a physical machine.

After disconnecting, the same ping sequence is run to capture how each layer responds.

---

**Step 3.2 - Ping the Public Internet After Disconnection**

```cmd
ping 8.8.8.8
```

<img width="823" height="210" alt="Ping internet after disrupting it" src="https://github.com/user-attachments/assets/0c263d19-ef9a-4313-9d98-44ddb4223392" />


>  All 4 packets failed with **General failure**. This is not the same as **Request timed out**. General failure means the operating system could not even attempt to send the packet as there is no usable network path at all. The adapter is disconnected and Windows has no route to the target. This confirms the fault is at the adapter/LAN layer, not at the internet or gateway level.

---

**Step 3.3 - Run ipconfig /release and ipconfig /renew**

```cmd
ipconfig /release
ipconfig /renew
```

<img width="875" height="248" alt="ip release and renew" src="https://github.com/user-attachments/assets/cc9d15cd-3738-4708-bf1c-7497783c59c6" />


>  Both commands return: **No operation can be performed on Ethernet0 while it has its media disconnected.** This is the expected result. The DHCP client cannot communicate with the DHCP server because there is no physical or virtual link. This output confirms that the adapter itself is the point of failure and eliminates DHCP misconfiguration as a cause.

---

**Step 3.4 - Run arp -a to Check Gateway Resolution**

```cmd
arp -a
```

<img width="764" height="77" alt="Arp result" src="https://github.com/user-attachments/assets/f6f08778-1d30-4fb1-9cbb-698f0b1ac1f1" />


>  **No ARP Entries Found.** Under normal conditions, the ARP cache would contain at least the MAC address of the default gateway [DEFAULT_GATEWAY], because the machine would have resolved it during normal communication. An empty ARP cache with a disconnected adapter confirms that no layer-2 communication has occurred since the adapter went down. The machine cannot resolve MAC addresses on the local segment because it has no link.

---

### Phase 4 - Restore Connectivity and Validate

**Step 4.1 - Reconnect the Network Adapter**

The virtual network adapter is re-enabled. The machine automatically re-establishes its DHCP lease and restores the link.

---

**Step 4.2 - Ping the Public Internet After Reconnection**

```cmd
ping 8.8.8.8
```

<img width="676" height="254" alt="ping internet after  reconnecting to the internet" src="https://github.com/user-attachments/assets/103fae72-263b-4076-9d1c-5836350817e1" />


>  4 out of 4 packets returned. Response times of 17–23ms confirm that connectivity is fully restored. The adapter re-joined the network, DHCP re-issued the lease and the internet path is clear. The diagnostic sequence is complete.

---

## Help Desk Ticket Note

```
──────────────────────────────────────────────────────────────────
TICKET #0054 | USER REPORTS INTERNET DOWN - CONNECTIVITY CHECK
Technician: Nnamso Mkpong | Date: March 2026 | Priority: STANDARD
──────────────────────────────────────────────────────────────────

ISSUE REPORTED:
  User on WIN11_CLIENT01 reports internet is not working.
  Unable to browse websites or reach external services.

INVESTIGATION — BASELINE (before fault):
  1. Ran ipconfig /all. Confirmed:
       IP: [CLIENT_IP], Gateway: [DEFAULT_GATEWAY],
       DNS: [DC_IP], DHCP: Enabled, Lease active.
  2. ping 127.0.0.1 - Pass (TCP/IP stack healthy).
  3. ping [CLIENT_IP] - Pass (NIC and IP binding healthy).
  4. ping [DEFAULT_GATEWAY] - Pass (LAN and gateway reachable).
  5. ping 8.8.8.8 - Pass (internet path working).
  Baseline confirmed: all layers functional before simulation.

FAULT SIMULATION:
  6. Disconnected virtual network adapter to simulate failure.
  7. ping 8.8.8.8 - FAIL: General failure (no route available).
  8. ipconfig /release - FAIL: Media disconnected.
  9. ipconfig /renew  - FAIL: Media disconnected.
  10. arp -a - No ARP entries. Gateway MAC not cached.
  Root cause: adapter disconnected / no physical link.

RESOLUTION:
  11. Reconnected the network adapter.
  12. DHCP lease automatically re-established.
  13. ping 8.8.8.8 - Pass. Connectivity restored.

OUTCOME:
  Internet connectivity confirmed restored. Root cause was
  a disconnected network adapter, not a software, DNS, or
  ISP fault. Diagnostic sequence correctly isolated the
  fault to the LAN/adapter layer in under 5 minutes.
──────────────────────────────────────────────────────────────────
```

---

## Diagnostic Findings Summary

| Test | Command | Before Fault | During Fault | After Restore |
|---|---|---|---|---|
| TCP/IP stack | `ping 127.0.0.1` |  Pass | Not re-tested |  Pass |
| Own IP / NIC | `[CLIENT_IP]` |  Pass | Not re-tested |  Pass |
| Default gateway | `ping [DEFAULT_GATEWAY]` |  Pass | Not re-tested |  Pass |
| Internet path | `ping 8.8.8.8` |  Pass |  General failure |  Pass |
| DHCP release | `ipconfig /release` | N/A |  Media disconnected | N/A |
| DHCP renew | `ipconfig /renew` | N/A |  Media disconnected | N/A |
| ARP cache | `arp -a` | Gateway cached |  No entries | Populated |

> The fault was isolated to the adapter/LAN layer. Layers 1 and 2 (TCP/IP stack and own IP) did not need retesting during the fault because **General failure** from ping and **media disconnected** from ipconfig already confirmed the adapter was the problem. Once the adapter was restored, a single internet ping was sufficient to confirm full recovery.

---

## Outcome and Validation

| Check | Result |
|---|---|
| ipconfig /all run and baseline values recorded | Pass |
| ping 127.0.0.1 confirms TCP/IP stack healthy | Pass |
| ping [DEFAULT_GATEWAY] confirms gateway reachable over LAN | Pass |
| ping 8.8.8.8 confirms internet path working at baseline | Pass |
| ping 8.8.8.8 returns General failure after adapter disconnect | Pass |
| ipconfig /release and /renew return media disconnected error | Pass |
| arp -a returns no entries confirming no LAN layer communication | Pass |
| ping 8.8.8.8 restored to 4/4 replies after adapter reconnected | Pass |

---

## What I Learned

1. **The ping sequence works from the inside out for a reason.** Starting at the loopback and working outward means you eliminate each layer before testing the next one. If loopback fails, nothing beyond it matters. If the gateway fails, testing the internet is pointless. The sequence is not arbitrary as it maps directly to the OSI model and tells you exactly where to stop looking.

2. **General failure and Request timed out mean different things.** General failure means the OS cannot even attempt to send the packet — there is no adapter, no route, or no link. Request timed out means the packet was sent but no reply came back as the device at the other end may be filtering pings, powered off, or unreachable. Knowing the difference stops you from drawing the wrong conclusion.

3. **ipconfig /release and /renew failing is useful diagnostic information.** Media disconnected from these commands confirms the adapter itself is down, not the DHCP server or the lease. This rules out a server-side DHCP fault without needing to check the server at all.

4. **An empty ARP cache tells a story.** The ARP table normally holds the MAC addresses of recently contacted devices, especially the gateway. If it is empty, the machine has not communicated on the local segment recently, which is consistent with an adapter that was just disconnected. In a real fault this would support the hypothesis before you even look at the cable.

5. **ipconfig /all at the start saves time throughout the investigation.** Having the IP, gateway, DNS, and DHCP values recorded before any changes means you always know what normal looks like. After restoring connectivity you can compare the new ipconfig /all against the baseline to confirm every value returned to its correct state.

---

## Real World Relevance

A user reporting that the internet is down is one of the most common helpdesk calls in any organisation. The instinct for many junior technicians is to restart the machine, blame the ISP, or raise a ticket with the network team. A technician who knows the four-step ping sequence can diagnose the fault in under two minutes without escalating.

The layered approach matters because the answer changes everything about the response. If the loopback fails, you are dealing with a software or driver problem that no cable change will fix. If the gateway fails but the own-IP ping passes, you are looking at a physical connectivity issue - cable, switch port, or adapter. If the gateway passes but the internet fails, the problem is outside the LAN and belongs with the ISP or the router's WAN configuration. Each layer produces a different resolution path.

In a managed environment with hundreds of machines, the ability to run this sequence remotely using tools like PowerShell remoting or a remote support agent means a technician can diagnose and often resolve network faults without ever touching the machine. The commands are the same. The sequence is the same. The skill scales from a single user's laptop to an enterprise fleet.

---

## Command Reference

| Command | Purpose | What a pass looks like | What a failure means |
|---|---|---|---|
| `ipconfig /all` | Full network configuration | IP, gateway, DNS all populated | Missing IP suggests DHCP failure or static misconfiguration |
| `ping 127.0.0.1` | Test local TCP/IP stack | 4/4 replies, <1ms | OS networking issue, reinstall TCP/IP stack |
| `ping [own IP]` | Test NIC and IP binding | 4/4 replies, <1ms | Adapter down or IP not assigned |
| `ping [gateway]` | Test LAN connectivity | 4/4 replies, <5ms | Cable, switch, or gateway issue |
| `ping 8.8.8.8` | Test internet path | 4/4 replies, 20–60ms typical | Router, ISP, or firewall issue |
| `ipconfig /release` | Release DHCP lease | Clears IP address | Media disconnected or DHCP server unreachable |
| `ipconfig /renew` | Request new DHCP lease | New IP assigned | DHCP server down, adapter disconnected |
| `arp -a` | View local MAC address cache | Gateway entry present | No LAN communication has occurred |

---

## Troubleshooting Reference

| Symptom | Likely Cause | Resolution |
|---|---|---|
| ping 127.0.0.1 fails | TCP/IP stack corrupted or networking service stopped | Reset TCP/IP stack: `netsh int ip reset`; restart machine |
| ping own IP fails but loopback passes | Adapter disabled or IP not assigned | Check adapter status in Device Manager; check DHCP lease |
| ping gateway fails but own IP passes | Physical link issue, wrong gateway, or switch fault | Check cable, switch port, gateway IP in ipconfig /all |
| ping 8.8.8.8 fails but gateway passes | ISP outage, router WAN fault, or firewall blocking | Check router WAN status; try alternate public IP e.g. 1.1.1.1 |
| General failure on ping | No adapter link at all | Check cable, enable adapter, check VM network settings |
| Request timed out on ping | Packet sent but no reply | Destination may block pings; try a different target |
| ipconfig /renew fails | Adapter disconnected or DHCP server unreachable | Restore adapter link first; then retry renew |
| ARP cache empty | No recent LAN communication | Expected when adapter is disconnected; repopulates automatically on reconnect |
