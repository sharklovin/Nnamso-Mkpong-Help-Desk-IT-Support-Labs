# Create and Close Tickets with SLA Notes

> **Author:** Nnamso Mkpong
>
> **Domain:** IT Service Desk - Ticket Management and SLA Documentation
>
> **Environment:** Ticket Log - simulating a service desk morning intake session
>
> **Completed:** April 2026

---

## Objective

Simulate a real service desk ticket workflow from intake to closure. Create five mock tickets covering common request types, assign correct categorisation, priority, impact, urgency and SLA class to each one, write structured investigation and action notes for one ticket through its full lifecycle and demonstrate the documentation standards required for SLA compliance and team handover quality.

---

## Business Scenario

> **Service Desk Morning Rush - April 7, 2026 | 08:00 onwards**
>
> You are the service desk analyst on first shift. Between 08:00 and 09:30 five requests arrive across email, the self-service portal, and a phone call. You must log each ticket correctly, assign the right priority so the SLA clock starts accurately and manage the queue so that the highest business impact issues receive first attention. One ticket must be escalated, one is awaiting user response and one must be worked through to full closure with complete notes.

This scenario reflects what a first-line support analyst faces every morning in any organisation running a formal ITIL-aligned service desk. The ability to log tickets correctly, assign the right priority without over or under-escalating, write notes that a colleague can pick up without a briefing and close tickets with evidence of resolution is a baseline competency for any help desk or IT support role.

---

## Environment and Tools Used

| Component | Detail |
|---|---|
| **Ticket Log Format** | Markdown based structured log |
| **SLA Framework** | 4 priority tiers: P1 Critical, P2 High, P3 Medium, P4 Low |
| **Categories Used** | Access Management, Hardware, Microsoft 365, Identity Management, Network and Connectivity |
| **Ticket Count** | Five tickets logged in this session |
| **Full Lifecycle Ticket** | TKT-0083 — Outlook PST Corruption and Send Receive Failure |
| **Escalated Ticket** | TKT-0085 — VPN Authentication Loop |
| **Awaiting User Response** | TKT-0084 — New User Account Setup |

---

## SLA Priority Framework - Key Concept

> **Assigning the wrong priority at intake is the single most common SLA failure in a service desk. Understanding the impact and urgency matrix prevents it.**

```
Priority Assignment Matrix

                    HIGH URGENCY          LOW URGENCY
                  (user cannot work)   (user can work around)

HIGH IMPACT     P1 CRITICAL             P2 HIGH
(multiple       Respond: 15 min         Respond: 1 hour
users or        Resolve: 4 hours        Resolve: 8 hours
business
critical)

LOW IMPACT      P2 HIGH                 P3 MEDIUM
(single user,   Respond: 1 hour         Respond: 4 hours
no workaround)  Resolve: 8 hours        Resolve: 24 hours

ROUTINE         P4 LOW
(planned work,  Respond: 8 hours
no urgency)     Resolve: 72 hours
```

Impact measures how many people or how much of the business is affected. Urgency measures how quickly the problem needs to be resolved before the impact gets worse. A P1 is not simply the loudest user as it is a confirmed high impact and high urgency combination. Every other ticket in the queue that is assigned P1 incorrectly steals response time from a real P1 and causes actual SLA breaches.

---

## Morning Queue — Ticket Register

The following five tickets were logged between 08:00 and 09:30 on April 7, 2026.

| Ticket ID | Summary | Category | Priority | Impact | Urgency | Status | SLA Response | SLA Resolve |
|---|---|---|---|---|---|---|---|---|
| TKT-0081 | Password reset - user locked out | Access Management | P2 High | Single user, no workaround | High | Resolved | 1 hour | 8 hours |
| TKT-0082 | Printer offline  finance dept | Hardware | P2 High | Team of 8, partial workaround | High | Resolved | 1 hour | 8 hours |
| TKT-0083 | Outlook PST corruption, send receive failing | Microsoft 365 | P2 High | Single user, no email workaround | High | **Closed** | 1 hour | 8 hours |
| TKT-0084 | New user account setup - starts Monday | Identity Management | P3 Medium | Planned work, no current impact | Low | **Awaiting User** | 4 hours | 24 hours |
| TKT-0085 | VPN authentication loop - remote user | Network and Connectivity | P2 High | Single remote user, completely blocked | High | **Escalated** | 1 hour | 8 hours |

---

## Ticket Summaries

---

### TKT-0081 — Password Reset

**Opened:** 08:04 | **Closed:** 08:19 | **Priority:** P2 High | **Status:** Resolved

**Reported by:** James Okafor | **Affected user:** James Okafor | **Department:** Operations

**Request summary:** User locked out of Windows account after three failed login attempts. Cannot access any systems. No workaround available as SSO is tied to the Windows account.

**Action taken:** Identity confirmed via employee ID and manager callback. Account unlocked via Active Directory Users and Computers. Temporary password issued using organisation password policy. User instructed to change password on next login. Advised on account lockout policy — three failed attempts triggers a 30 minute lockout.

**Resolution:** Account unlocked and user confirmed successful login at 08:19. Total handling time: 15 minutes.

**SLA status:** Response within SLA. Resolved within SLA. No breach.

---

### TKT-0082 — Printer Offline — Finance Department

**Opened:** 08:31 | **Closed:** 09:10 | **Priority:** P2 High | **Status:** Resolved

**Reported by:** Sandra Eze | **Affected users:** Finance team (8 people) | **Department:** Finance

**Request summary:** Network printer on the second floor (HP LaserJet — IP 192.168.1.45) is showing offline to all eight finance team members. Month-end reporting is this week and the team uses the printer for sign-off documents. Partial workaround available: users can save as PDF and email for signature, but this adds significant process overhead.

**Action taken:** Checked printer status via print server — printer was showing as offline in the Windows print queue. Physically checked the device. Found the printer had lost its network lease — the IP address had been reassigned by DHCP following a router restart over the weekend. Assigned a static IP to the printer at the print server. Cleared all stuck jobs from the queue. Confirmed test page printed successfully. Informed the network team of the DHCP lease reassignment issue and recommended a static IP reservation for all shared printers.

**Resolution:** Printer online and printing at 09:10. All eight users confirmed printer accessible. Total handling time: 39 minutes.

**SLA status:** Response within SLA. Resolved within SLA. No breach.

---

### TKT-0083 — Outlook PST Corruption — Full Lifecycle (See TICKET-0083-outlook-pst.md)

**Opened:** 08:47 | **Closed:** 11:23 | **Priority:** P2 High | **Status:** Closed

**Reported by:** Adaeze Nwosu | **Affected user:** Adaeze Nwosu | **Department:** Legal

Full investigation notes, first response, action log, escalation assessment, user confirmation, and closure note are documented in the dedicated ticket file `TICKET-0083-outlook-pst.md`.

**SLA status:** Response within SLA. Resolved within SLA. No breach.

---

### TKT-0084 — New User Account Setup

**Opened:** 09:05 | **Priority:** P3 Medium | **Status:** Awaiting User Response

**Reported by:** HR Portal — automated submission | **Requestor:** HR Department | **New starter:** Chiamaka Bello | **Start date:** Monday April 14, 2026

**Request summary:** New employee starting Monday requires Windows account, email, Microsoft 365 licence, and access to the Legal shared drive. Start date is one week away. No current business impact — this is planned provisioning work.

**Action taken:** Ticket logged as P3 Medium — planned work with no current urgency. First response sent to HR at 09:22 requesting confirmation of the following: full legal name as it should appear in Active Directory, job title and department, line manager name and email, specific shared drives and applications required, and whether a laptop needs to be provisioned or if the user is bringing their own device.

**Current status:** Awaiting HR response. Ticket placed in Awaiting User state. SLA clock paused per policy while awaiting user-supplied information. SLA will resume when HR responds.

**Note for handover:** If HR has not responded by end of business April 8, send a chase email and escalate to the HR manager if the start date is at risk. Account creation must be completed by Thursday April 10 to allow time for laptop build and access testing before Monday.

**SLA status:** Within SLA. Response sent within 4 hours. Awaiting user response — SLA paused.

---

### TKT-0085 — VPN Authentication Loop

**Opened:** 09:22 | **Priority:** P2 High | **Status:** Escalated to Network Team

**Reported by:** Phone call | **Affected user:** Emmanuel Chukwu | **Department:** Sales | **Location:** Remote (working from home)

**Request summary:** Remote user is unable to connect to VPN. Client shows connected briefly then disconnects in a loop. User cannot access any company systems. Has tried restarting the VPN client, clearing credentials, and rebooting their laptop. Issue began this morning — worked correctly on Friday.

**Action taken at first line:** Confirmed user identity and remote setup. Guided user through credential re-entry and VPN client reinstall — issue persisted. Checked the VPN gateway status page — no publicly reported outage. Reviewed the user's account in Active Directory — account active, not locked, VPN group membership confirmed. Reviewed DNS resolution on the user's machine — resolving correctly. Cleared VPN client certificate cache. Issue persisted after all first-line steps.

**Escalation decision:** Issue is beyond first-line resolution scope. Network infrastructure team required to review VPN gateway logs and user authentication trace. Escalated to L2 Network Team at 09:58 with full notes.

**Escalation note passed to L2:**

User Emmanuel Chukwu (Sales, remote) unable to connect to VPN since this morning. Authentication loop confirmed. First-line actions completed: credentials re-entered, VPN client reinstalled, account status verified in AD (active, correct group membership), DNS resolving correctly, certificate cache cleared. Issue persists. Gateway logs and authentication trace required. User is completely blocked from all company systems. P2 SLA applies — resolve target 17:00 today. User contact: e.chukwu@company.com | 07xxx xxxxxx.

**Current status:** Escalated to L2 Network Team at 09:58. Ticket ownership transferred. First-line SLA fulfilled — response and escalation within P2 window.

**SLA status:** Within SLA at time of escalation. L2 team now owns the resolve target.

---

## Full Lifecycle Ticket

See dedicated file `TICKET-0083-outlook-pst.md` for the complete ticket lifecycle documentation including first response, investigation notes, action log, escalation assessment, user confirmation, and formal closure note.

---

## How Clean Ticket Notes Protect SLA Performance and Team Handover Quality

> **A ticket note is not a diary entry. It is a formal record that must allow any technician to pick up the ticket and continue without a briefing.**

Clean ticket notes protect SLA performance in three specific ways.

The first is continuity. When a ticket is handed over between shifts, escalated to a different team, or picked up by a colleague after a break, every minute spent asking the previous technician what they did is a minute that counts against the SLA clock. A ticket with complete notes — what was reported, what was tested, what was found, what was done, and what the next action is — allows immediate continuation. A ticket with only "spoke to user, looking into it" causes delays and errors.

The second is accountability. In a managed service environment, SLA performance is measured and reported. When a breach occurs, the ticket log is the primary evidence for a post-incident review. Clear timestamps on every action show exactly when the ticket was received, when the first response went out, when each diagnostic step was taken, and when the issue was resolved. Gaps in the timestamp record make SLA defence impossible and expose the analyst and the team to unfair criticism.

The third is pattern recognition. Individual tickets tell individual stories. Aggregated ticket notes tell operational stories. A service desk manager reviewing a week of ticket logs should be able to identify recurring failure categories, frequently affected users, applications generating repeated incidents, and hardware reaching end of life — purely from reading structured ticket notes. Unstructured notes ("fixed the thing, user happy") contribute nothing to this operational intelligence.

---

## Steps Performed

1. Opened the ticket log template and set up the morning session header with date, analyst name, and shift start time.
2. Logged all five incoming tickets between 08:00 and 09:30 with full categorisation, priority, impact, urgency, and SLA class.
3. Resolved TKT-0081 (password reset) within 15 minutes and documented the action and resolution.
4. Resolved TKT-0082 (printer offline) after identifying a DHCP lease reassignment fault and documenting the fix and preventive recommendation.
5. Worked TKT-0083 (Outlook PST) through full lifecycle — first response, investigation, action log, escalation assessment, user confirmation, and closure note.
6. Logged TKT-0084 (new user) as P3 Medium, sent first response requesting HR information, and placed ticket in Awaiting User state with a handover note.
7. Escalated TKT-0085 (VPN loop) to L2 Network Team after completing all first-line diagnostic steps, with a full handover note passed at time of escalation.
8. Reviewed all five tickets for timestamp completeness, status accuracy, and SLA compliance.

---

## Outcome and Validation

| Check | Result |
|---|---|
| Five tickets logged with full categorisation and SLA class | Pass |
| Priority assigned correctly using impact and urgency matrix | Pass |
| TKT-0081 resolved within P2 SLA window | Pass |
| TKT-0082 resolved within P2 SLA window with preventive recommendation | Pass |
| TKT-0083 closed with full lifecycle documentation | Pass |
| TKT-0084 placed in Awaiting User state with handover note | Pass |
| TKT-0085 escalated with complete L2 handover note | Pass |
| All tickets show clear ownership, status, and timestamps | Pass |
| Ticket notes support SLA defence without verbal briefing | Pass |

---

## What I Learned

1. **Priority is not about how angry the user is.** It is a structured assessment of impact and urgency. A user who calls loudly about a P3 issue should still receive a P3 response time. Assigning P1 to calm the user down or to show responsiveness corrupts the queue, causes real P1 tickets to wait longer, and produces SLA data that does not reflect actual service performance.

2. **The SLA clock starts at ticket creation, not at first contact.** A ticket that sits unlogged for 20 minutes before it is entered into the system has already consumed 20 minutes of the response window. Logging tickets immediately, even with incomplete information, is better than waiting until all details are gathered before creating the record.

3. **Awaiting User is a legitimate ticket state, not an excuse.** When a ticket genuinely requires information from the user before work can continue, placing it in Awaiting User and pausing the SLA clock is correct procedure. However, the ticket still requires a chase mechanism and a handover note so it does not sit forgotten until the user calls back frustrated.

4. **Escalation is a documented decision, not an admission of failure.** A well-documented escalation shows that first-line steps were completed correctly, that the technician recognised the boundary of their scope, and that the receiving team has everything they need to continue. An escalation with no notes is the real failure — it forces the L2 team to start from scratch and wastes the work already done.

5. **Closure notes are the most commonly skipped and most professionally damaging part of ticket hygiene.** Closing a ticket with no resolution note means the next ticket for the same user or the same device has no history to reference. Closure notes should always record what the root cause was, what the fix was, and whether the user confirmed the issue was resolved.

---

## Real World Relevance

Ticket management is the operational backbone of every IT support function. Every troubleshooting skill, every technical qualification, and every diagnostic framework a technician learns is only useful if the outcomes are recorded in a way that the organisation can build on. A technician who resolves ten issues per day but leaves no documentation creates a team that cannot grow, cannot improve its SLA performance, and cannot demonstrate its value to the business.

In technical interviews for help desk and service desk roles, ticket writing is consistently tested — either through a roleplay scenario or by asking the candidate to describe what a complete ticket note looks like. Candidates who can articulate the difference between a first response note, an investigation note, and a closure note, and who understand why each one exists, demonstrate a level of professional maturity that separates them from candidates who treat tickets as a checkbox.

The SLA framework in this lab reflects real ITIL-aligned service desk practice. P1 through P4 priority tiers, impact and urgency matrices, Awaiting User state management, and escalation documentation are all standard components of service desk platforms including ServiceNow, Jira Service Management, Freshdesk, and Zendesk. The principles applied in a Markdown ticket log are directly transferable to any of these platforms.

---

## Troubleshooting Reference

| Situation | Correct Action | Common Mistake |
|---|---|---|
| User reports urgent issue but impact is single user with workaround | Assign P3 Medium | Assigning P1 or P2 because user is stressed |
| Ticket requires information from user before work can start | Log ticket, send first response, set Awaiting User, pause SLA | Leaving ticket unlogged until user responds |
| First-line steps exhausted, issue not resolved | Escalate with full notes, confirm L2 ownership | Closing ticket as unresolved or leaving in queue |
| User says issue is resolved but you have not confirmed | Request user confirmation before closing | Closing ticket based on user verbal with no written confirmation |
| Same fault appears on multiple tickets in the same week | Flag as potential problem record, notify team lead | Treating each ticket in isolation |
| New user request arrives with a future start date | Log as P3 or P4, confirm provisioning requirements, set deadline reminder | Logging as P1 because it feels urgent to the requestor |
