# Morning Ticket Queue - April 7, 2026

**Analyst:** Nnamso Mkpong
**Shift Start:** 08:00
**Session Period:** 08:00 to 11:30
**Total Tickets Logged:** 5
**Tickets Resolved This Session:** 2 (TKT-0081, TKT-0082)
**Tickets Closed This Session:** 1 (TKT-0083 — full lifecycle)
**Tickets Awaiting User:** 1 (TKT-0084)
**Tickets Escalated:** 1 (TKT-0085)

---

## Queue Summary

| Ticket ID | Time Opened | Summary | Priority | Status | SLA |
|---|---|---|---|---|---|
| TKT-0081 | 08:04 | Password reset - James Okafor locked out | P2 High | Resolved | Met |
| TKT-0082 | 08:31 | Printer offline - Finance team HP LaserJet | P2 High | Resolved | Met |
| TKT-0083 | 08:47 | Outlook PST path broken - Adaeze Nwosu Legal | P2 High | Closed | Met |
| TKT-0084 | 09:05 | New user setup - Chiamaka Bello starts April 14 | P3 Medium | Awaiting User | On track |
| TKT-0085 | 09:22 | VPN auth loop - Emmanuel Chukwu remote sales | P2 High | Escalated to L2 | Met at L1 |

---

---

## TKT-0081 — Password Reset

**Opened:** 08:04 | **Closed:** 08:19 | **Total Time:** 15 minutes

### Classification

| Field | Value |
|---|---|
| Category | Access Management |
| Subcategory | Account Lockout |
| Priority | P2 High |
| Impact | Single user - no workaround, all systems inaccessible |
| Urgency | High - user cannot start work |
| SLA Response Target | 1 hour |
| SLA Resolve Target | 8 hours |

### Reported by

James Okafor - Operations Department - reported via phone at 08:04.

### Issue Description

User is locked out of his Windows account after three failed login attempts at 07:58. Cannot access any company systems. Single sign-on is tied to the Windows account so all applications are inaccessible.

### First Response

Ticket logged immediately on phone. User informed that account unlock is being actioned now. No formal written first response required for phone-in — verbal acknowledgement at 08:04 satisfies P2 response requirement.

### Investigation

No investigation required. Account lockout is a self-service preventable fault. Confirmed via Active Directory that the account is locked and that three failed attempts were logged at 07:56, 07:57, and 07:58. No suspicious activity — failed attempts occurred from the user's own machine.

### Action Taken

08:06 - Identity verified via employee ID (EMP-0182) and callback to line manager (Bola Adeyemi confirmed identity).

08:07 - Account unlocked in Active Directory Users and Computers. Temporary password issued per policy: minimum 12 characters, complexity enforced, must change at next login.

08:09 - Temporary password communicated to user via phone. User confirmed successful login at 08:12.

08:14 - Advised user on account lockout policy: three failed attempts triggers lockout. Recommended using the password manager to avoid future lockouts. Reminded user that the self-service password reset portal is available at the intranet link if this occurs out of hours.

### Resolution

Account unlocked. User confirmed access restored at 08:12. Ticket closed at 08:19 after documentation completed.

### Closure Note

Account lockout resolved. Identity verified via employee ID and manager callback. Account unlocked in AD. Temporary password issued. User confirmed access at 08:12. No data loss. No suspicious activity. User advised on lockout policy and self-service reset portal. No escalation required.

**SLA:** Response at 08:04 (0 minutes). Resolved at 08:12 (8 minutes). Well within P2 targets. No breach.

---

---

## TKT-0082 - Printer Offline — Finance Department

**Opened:** 08:31 | **Closed:** 09:10 | **Total Time:** 39 minutes

### Classification

| Field | Value |
|---|---|
| Category | Hardware |
| Subcategory | Network Printer |
| Priority | P2 High |
| Impact | Finance team of 8 — partial workaround via PDF email |
| Urgency | High — month-end reporting week |
| SLA Response Target | 1 hour |
| SLA Resolve Target | 8 hours |

### Reported by

Sandra Eze - Finance Department — reported via service portal at 08:31.

### Issue Description

HP LaserJet network printer on the second floor (last known IP 192.168.1.45) is showing as offline to all eight Finance team members. All print jobs are queuing and not printing. The team is in month-end reporting week and uses the printer for physical sign-off documents. Partial workaround available (PDF email) but it adds process overhead and slows sign-off workflow.

### First Response

**Sent at 08:39 - 8 minutes after intake:**

> Hi Sandra, thank you for raising this. Ticket TKT-0082 is open and I am investigating the network printer now. I will update you within 30 minutes. If the issue is affecting any time-sensitive reporting today, please use the PDF save and email workaround in the meantime.

### Investigation

08:42 - Checked print server (PRTSVR01) via remote management. HP LaserJet at 192.168.1.45 showing offline. Attempted ping to 192.168.1.45 — no response.

08:45 - Checked DHCP server lease table (DHCP-SRV01). Found that IP address 192.168.1.45 had been released after the weekend router restart on Saturday April 5 at 02:14. The IP had been reassigned by DHCP to a different device (workstation WIN11-FIN-07) at 07:43 this morning.

08:48 - Checked the printer's current network address by physically accessing the device on the second floor. Printed a configuration page from the printer's control panel. Printer had obtained a new DHCP address: 192.168.1.108.

Root cause confirmed: The printer lost its DHCP lease over the weekend and obtained a new IP address this morning. The print server and all client machines still reference the old IP (192.168.1.45) which now belongs to a different device.

### Action Taken

08:52 - Updated printer port on PRTSVR01 to point to the new IP (192.168.1.108). Confirmed printer visible on network.

08:55 - Cleared all 23 stuck jobs from the print queue that had accumulated since the printer went offline.

08:57 - Sent a test page from the print server. Confirmed printed successfully on the second floor printer.

09:00 - Assigned a static IP address to the printer at the router (192.168.1.45 reserved for the printer MAC address) so the printer always receives the same IP address regardless of DHCP lease events.

09:02 - Updated print server port back to 192.168.1.45 and confirmed printer still responds correctly with the static reservation in place.

09:05 - Notified Sandra that the printer is online and that users can retry their print jobs. Cleared queue — users will need to resubmit any jobs that were in the queue.

09:08 - Sandra confirmed all eight team members can print. Test page confirmed at the printer.

### Preventive Recommendation

Submitted a recommendation to the network team: all shared printers and shared networked devices should have DHCP static reservations applied at the router level. Dynamic DHCP for these devices creates a recurring fault risk after any DHCP lease event. This is a quick change (MAC address to IP mapping in the router) that eliminates this entire category of ticket.

### Closure Note

Network printer fault resolved. Root cause was DHCP lease loss over the weekend router restart causing the printer to obtain a new IP address. Print server was still pointing to the old IP. Static IP reservation applied. Print server port updated. Queue cleared. All eight Finance team members confirmed printing at 09:08. Preventive recommendation submitted to network team.

**SLA:** First response at 08:39 (8 minutes). Resolved at 09:08 (37 minutes). Well within P2 targets. No breach.

---

---

## TKT-0083 - Outlook PST Corruption

**See dedicated file:** `TICKET-0083-outlook-pst.md`

**Summary:** Outlook profile data file path broken by OneDrive known-folder protection event over the weekend. PST file found at AppData\Local instead of Documents path referenced in profile. Profile updated, PST moved to C:\OutlookData\ outside OneDrive sync scope. Scanpst confirmed no corruption. User confirmed resolution at 10:10. Closed at 11:23.

**SLA:** Response at 08:52 (5 minutes). Resolved at 10:05 (1 hour 18 minutes). No breach.

---

---

## TKT-0084 - New User Account Setup

**Opened:** 09:05 | **Status:** Awaiting User Response | **SLA:** Paused pending HR response

### Classification

| Field | Value |
|---|---|
| Category | Identity Management |
| Subcategory | New User Provisioning |
| Priority | P3 Medium |
| Impact | No current impact — new starter not yet on site |
| Urgency | Low — start date is Monday April 14 (one week away) |
| SLA Response Target | 4 hours |
| SLA Resolve Target | 24 hours (provisioning must be complete by April 10) |

### Reported by

HR Portal - automated submission — 09:05. New starter: Chiamaka Bello. Department: Legal. Start date: Monday April 14, 2026.

### Issue Description

New employee joining the Legal department on April 14 requires full account provisioning: Windows domain account, email address, Microsoft 365 E3 licence, access to the Legal team shared drive (\\FILESVR01\Legal), and a laptop if not already assigned.

### First Response

**Sent at 09:22 - 17 minutes after intake:**

> Hi HR Team, thank you for submitting the new starter request for Chiamaka Bello.
>
> I have logged this as TKT-0084 and I am targeting completion of all provisioning by Thursday April 10 to allow time for device setup and access testing before the start date.
>
> To proceed, please confirm the following:
>
> Full legal name as it should appear in Active Directory and on the email address. Job title and official department name. Line manager name and email address (required for account approval). Specific shared drives, applications, or systems beyond the Legal team share that will be required. Whether a company laptop needs to be provisioned or whether the user is bringing their own device. Whether a mobile phone or other device provisioning is required.
>
> Please respond by end of business April 8 so provisioning can be completed on time.
>
> Nnamso Mkpong — IT Support

### Current Status

Awaiting HR response. Ticket placed in Awaiting User state. SLA clock paused per service desk policy while awaiting requestor-supplied information.

### Handover Note for Next Analyst

If HR has not responded by 17:00 April 8, send a chase email directly to the HR manager. If no response by morning of April 9, escalate to the IT team lead so the provisioning deadline can be assessed. Account creation and licence assignment must be completed by April 10 to allow 48 hours for laptop build and testing before April 14 start date.

Required provisioning steps when HR responds:

Step one: Create Active Directory account with correct name, department, and manager.
Step two: Assign Microsoft 365 E3 licence in the M365 admin centre.
Step three: Add user to the Legal shared drive security group on FILESVR01.
Step four: Build or assign laptop and apply baseline configuration.
Step five: Test login and application access before handover to user on April 14.
Step six: Update ticket with all provisioning confirmations and close on start date.

---

---

## TKT-0085 — VPN Authentication Loop

**Opened:** 09:22 | **Escalated:** 09:58 | **Status:** Escalated to L2 Network Team

### Classification

| Field | Value |
|---|---|
| Category | Network and Connectivity |
| Subcategory | VPN Client — Authentication Failure |
| Priority | P2 High |
| Impact | Single remote user — completely blocked from all company systems |
| Urgency | High — no workaround, user cannot work |
| SLA Response Target | 1 hour |
| SLA Resolve Target | 8 hours (by 17:00 April 7) |

### Reported by

Emmanuel Chukwu — Sales Department — phone call at 09:22. User is working from home.

### Issue Description

Remote user is unable to connect to the company VPN. The Cisco AnyConnect client shows "Connected" briefly then disconnects after approximately 3 seconds, entering a continuous connect and disconnect loop. The user cannot access any company systems including email, CRM, and file shares. The issue began this morning. VPN was functioning correctly on Friday April 4.

The user has independently attempted: restarting the VPN client, re-entering credentials, and rebooting the laptop. None resolved the issue.

### First Response

Ticket logged while user was on the phone at 09:22. Verbal acknowledgement given immediately. Written response sent at 09:30:

> Hi Emmanuel, I have logged this as TKT-0085 and I am working on it now. I will keep you updated by phone and on this ticket. Please keep your laptop on and available for remote diagnostics.

### Investigation Log

**09:24 — Account status check**

Checked Emmanuel Chukwu's account in Active Directory. Account is active. No lockout. VPN security group membership confirmed (GRP-VPN-USERS). Password not expired.

**09:28 — VPN gateway status check**

Checked the VPN gateway status dashboard at the internal monitoring URL. No reported outage. Other remote users are connecting successfully — confirmed with two colleagues in Sales who are also working from home today.

**09:33 — Remote diagnostics with user**

Connected to the user's machine via a direct session initiated through the endpoint management console (does not require VPN). Observed the AnyConnect client in the loop state.

Reviewed AnyConnect diagnostic logs at `C:\Users\Emmanuel.Chukwu\AppData\Local\Cisco\Cisco AnyConnect Secure Mobility Client\`. Log shows repeated "Authentication failed" entries — not a "Connection timeout" or "Network unreachable" pattern. The client is reaching the gateway but authentication is failing after the initial handshake.

**09:40 — Credential re-entry test**

Cleared stored credentials from the AnyConnect credential store. User re-entered username and password manually. Same result — authenticated briefly then disconnected.

**09:44 — VPN client reinstall**

Uninstalled Cisco AnyConnect via Apps and Features. Downloaded fresh installer from the internal software repository. Reinstalled. Configured VPN gateway address. Test connection — same authentication loop result.

**09:50 — DNS and certificate check**

Confirmed DNS resolution on the user's machine — resolving internal gateway address correctly. Checked certificate store — VPN client certificate present and not expired (valid until December 2026). Cleared certificate cache and retested — same result.

**09:55 — Gateway-side investigation required**

All first-line diagnostic steps exhausted. The issue is occurring at the gateway authentication layer, which requires access to the VPN server logs and the RADIUS authentication trace. This is outside L1 scope.

**Escalation decision made at 09:58.**

### Escalation Note Passed to L2 Network Team

**Escalated at:** 09:58 via ticketing system and direct Slack message to the Network Team channel.

> TKT-0085 — VPN Authentication Loop — Escalation to L2 Network Team
>
> User: Emmanuel Chukwu | Sales | Remote working from home
>
> Symptom: Cisco AnyConnect connects then drops in a 3 second loop. Authentication failed entries in AnyConnect log. Issue started this morning. Worked correctly Friday April 4.
>
> L1 steps completed and confirmed not the cause:
>
> Active Directory account status — active, not locked, VPN group membership confirmed, password not expired.
>
> VPN gateway status — no reported outage, other remote users connecting successfully.
>
> AnyConnect log review — "Authentication failed" pattern, not network or timeout.
>
> Credential re-entry — cleared stored credentials, re-entered manually, same result.
>
> VPN client reinstall — fresh install from internal repo, same result.
>
> DNS resolution — confirmed resolving gateway address correctly.
>
> Certificate — present and valid until December 2026, cache cleared, same result.
>
> Next steps required: Review VPN gateway logs for Emmanuel Chukwu authentication trace. Check RADIUS authentication server for failed auth entries linked to this account. Check if any policy change was applied to this user or the Sales VPN group over the weekend.
>
> P2 SLA — resolve target: 17:00 today. User is completely blocked from all company systems.
>
> User contact: e.chukwu@company.com | 07xxx xxxxxx
>
> Remote access to user machine available via endpoint management console — does not require VPN.
>
> Nnamso Mkpong — IT Support L1

### Handover Note

L2 Network Team owns the resolve target from 09:58. L1 SLA obligation (first response and escalation within 1 hour) is met. If L2 has not provided an update by 13:00, follow up on the Slack thread and update the ticket with the L2 status so the user has visibility.

**SLA status at escalation:** Response at 09:22 (0 minutes, phone in hand). Escalation completed at 09:58 (36 minutes). Well within P2 response window. Resolve target now owned by L2.

---

## Session Close Summary — 11:30

| Ticket | Final Status | SLA |
|---|---|---|
| TKT-0081 | Resolved — 08:19 | Met |
| TKT-0082 | Resolved — 09:08 | Met |
| TKT-0083 | Closed — 11:23 (full lifecycle documented) | Met |
| TKT-0084 | Awaiting User — SLA paused | On track |
| TKT-0085 | Escalated to L2 — 09:58 | Met at L1 |

**SLA breach count this session:** 0

**Actions outstanding at session end:**

TKT-0084 — Chase HR by 17:00 April 8 if no response received. Escalate to IT lead if provisioning deadline is at risk.

TKT-0085 — Follow up with L2 Network Team by 13:00 for a status update. Update ticket and inform user of progress.

**Session notes for next analyst:**

All tickets in queue are documented with full notes. TKT-0083 has a dedicated full lifecycle file (TICKET-0083-outlook-pst.md). TKT-0085 escalation notes have been passed to the L2 Network Team channel with full context. TKT-0084 requires an HR response chase if not received by end of business April 8.

**Documented by:**
Nnamso Mkpong
IT Support Portfolio
