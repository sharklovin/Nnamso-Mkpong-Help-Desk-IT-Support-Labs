# Event Viewer and Startup Issue Analysis

> **Author:** Nnamso Mkpong
>
> **Domain:** Windows 11 - Startup Performance and Event Log Diagnostics
>
> **Environment:** Windows 11 Client, Task Manager Startup Apps, Event Viewer, Windows Application Log
>
> **Completed:** April 2026

---

## Objective

Use Task Manager's Startup Apps tab and Windows Event Viewer to investigate a slow boot and application crash at login. Identify the startup item responsible for the performance impact, disable it, capture the supporting Event Viewer log entry that confirms the root cause, and record the findings as a structured incident report.

---

## Business Scenario

> **Ticket #0061 | Slow Boot and Application Crash at Login - Remote Workstation**
>
> A user reports that their PC takes too long to boot and that an application crashes at login every morning. The issue has been present since a recent software installation. IT support has been asked to investigate whether the fault is in the startup configuration, a failing application process or a deeper system event that requires escalation.

Slow boot complaints are among the most frequently raised tickets in any managed Windows environment. They are often treated as low priority until they accumulate into lost productivity across multiple users. In this case, an application crash at login elevates the ticket because it means the user cannot start their primary workflow until the issue is resolved. Speed and structured diagnosis are essential.

---

## Environment and Tools Used

| Component | Detail |
|---|---|
| **Client OS** | Windows 11 |
| **Primary Tool** | Task Manager → Startup Apps tab |
| **Log Tool** | Event Viewer → Windows Logs → Application |
| **Key Event** | Event ID 4096 - VBScriptDeprecationAlert - Source: VBScriptDeprecationAlert |
| **Identified Process** | BlueStacksServices.exe (106 child processes) |
| **Computer Name** | Shark007 |

---

## Steps Performed

---

### Phase 1 - Record the Startup Baseline

**Step 1.1 - Open Task Manager and Navigate to Startup Apps**

Open Task Manager using `Ctrl + Shift + Esc`. Click **Startup apps** in the left navigation panel.

Review the full list, paying attention to three columns: **Status**, **Startup impact**, and the presence of a **Publisher** field. Items with no publisher and high startup impact are the first candidates to investigate.
<img width="854" height="581" alt="01 Open start up" src="https://github.com/user-attachments/assets/fe279b3f-2582-424f-a189-0225fc2ee1d6" />

---

### Phase 2 - Disable the Non-Essential Startup Item

**Step 2.1 - Disable BlueStacksServices.exe**

Right-click **BlueStacksServices.exe** in the Startup Apps list and select **Disable** or select it and click the **Disable** button in the toolbar. Confirm the status column changes from Enabled to Disabled.

<img width="1363" height="721" alt="02 Blue stack disable " src="https://github.com/user-attachments/assets/b6826733-c119-4fb0-88a0-62771b49b34a" />


> **Highlighted:**
> - **BlueStacksServices.exe** now shows **Disabled** status.

> Disabling non-essential startup items does not uninstall the applications. They remain available to launch manually. The change only prevents them from consuming system resources during the boot and login sequence.

---

### Phase 3 - Investigate in Event Viewer

**Step 3.1 - Open Event Viewer and Navigate to Application Log**

Open Event Viewer by pressing `Windows + R`, typing `eventvwr.msc`, and pressing Enter. In the left panel, expand **Windows Logs** and click **Application**. Sort by **Date and Time** descending to show the most recent events first.

Filter for **Warning** and **Error** level events around the time of the reported fault. Look specifically for events that reference the processes identified in the Startup Apps tab.

<img width="1108" height="698" alt="03 Event manager warning for blue stack" src="https://github.com/user-attachments/assets/23c11d4b-79d4-4af2-9f1d-082ce6ddf44d" />


> **Highlighted:**
> - The **Warning row** in the Application log identifies source `VBScriptDeprecationAlert` with Event ID 4096 - logged at the exact time the user reported the crash at login.
> - The **ProcessTree field** in the event detail is the critical evidence. It reads:
>   `cscript.exe;BlueStacksServices.exe;explorer.exe;userinit.exe;winlogon.exe`
>   This confirms that BlueStacksServices.exe spawned a VBScript process (`cscript.exe`) during the Windows login sequence (`winlogon.exe → userinit.exe → explorer.exe`). That script triggered a deprecation warning in Windows 11, which caused the crash at login.
> - **Event ID 4096 from VBScriptDeprecationAlert** means Windows 11 is warning that VBScript — a scripting language being phased out / was invoked by BlueStacks during startup. In newer Windows 11 builds, this can cause the invoking process to fail.

> This event is the log-level confirmation that BlueStacksServices.exe is directly responsible for both the slow boot (high startup impact) and the application crash at login (VBScript invocation during winlogon). The diagnosis is confirmed at two layers.

---

### Phase 4 - Validate and Document

**Step 4.1 - Reboot and Compare**

After disabling BlueStacksServices.exe and the other non-essential items, reboot the machine. On the next boot, re-open Task Manager → Startup Apps and note the Last BIOS time. Then observe the time from the login screen to a fully usable desktop.

Expected result: The Windows load time after login should be noticeably shorter with BlueStacks and other high-impact items disabled. The application crash at login should not recur because BlueStacksServices.exe is no longer running at startup and therefore cannot invoke the VBScript process during winlogon.

**Step 4.2 - Confirm No Recurrence in Event Viewer**

After the reboot, return to Event Viewer → Application log and confirm that no new VBScriptDeprecationAlert events appear in the log around the time of the new boot. The absence of Event ID 4096 after the fix is part of the validation evidence.

---

## Business Impact Note

> **Slow boot complaints have a compounding effect on productivity that is not always visible in a single ticket.**

A user who waits an extra 3 to 5 minutes every morning to reach a usable desktop loses approximately 15 - 25 minutes per week to a fault that takes under 10 minutes to diagnose and fix. When an application crash at login is added to this, the user may be losing a further 5–10 minutes attempting to relaunch their tools before they can begin any work.

For a technician, this category of ticket is high-value because the fix is fast, the evidence is clear, and the result is immediately visible to the user. Startup performance issues that are resolved quickly and documented properly also feed into asset management - if multiple machines are running the same non-essential high-impact startup items, a single policy change across the fleet can eliminate the entire ticket category.

The Event Viewer confirmation in this lab adds an additional dimension. Event ID 4096 with a BlueStacks ProcessTree is not just a startup performance issue — it is evidence of a deprecated scripting method being invoked during the Windows login sequence, which in future Windows 11 updates may become a hard block rather than a warning. Documenting it now and raising it to the appropriate team prevents a future escalation.

---

## Help Desk Ticket Note

See `TICKET-0061-startup-event-analysis.md` in this folder.

---

## Outcome and Validation

| Check | Result |
|---|---|
| Startup Apps baseline recorded - BlueStacksServices.exe identified as High impact | Pass |
| Last BIOS time recorded at baseline: 12.4 seconds | Pass |
| BlueStacksServices.exe disabled in Startup Apps | Pass |
| Additional non-essential items disabled (Spotify, Teams, Telegram, Xbox, etc.) | Pass |
| Event Viewer opened - Application log reviewed | Pass |
| Event ID 4096 - VBScriptDeprecationAlert - recorded at 4/3/2026 3:30:20 PM | Pass |
| ProcessTree in event detail confirms BlueStacksServices.exe as root cause | Pass |
| Root cause confirmed at two diagnostic layers: Startup impact + Event log | Pass |
| Reboot scheduled - startup time and crash recurrence to be verified | Pending reboot |

---

## What I Learned

1. **Task Manager's Startup Apps tab is the fastest first step for any slow boot complaint.** The Startup Impact column categorises items as High, Medium, Low, or Not Measured, and the Last BIOS time at the top separates hardware delays from Windows load delays. These two data points alone narrow most slow boot investigations to a specific item within 60 seconds.

2. **A process with no publisher and a High startup impact is always worth investigating.** BlueStacksServices.exe running 88–106 child processes at boot with no registered publisher is a clear signal. It does not mean it is malicious, but it does mean it is not a Windows or business-critical service and is a safe candidate for disabling in a lab or managed environment.

3. **Event Viewer confirms what Task Manager suggests.** Disabling a startup item based on its impact rating alone is a reasonable fix, but Event ID 4096 with the BlueStacks ProcessTree turns a presumption into a confirmed finding. In a ticket, the difference between "I disabled it because it looked heavy" and "Event ID 4096 at 3:30 PM confirms BlueStacks invoked a deprecated VBScript during winlogon" is the difference between a guess and a diagnosis.

4. **VBScriptDeprecationAlert events are a forward-looking risk, not just a current fault.** Windows 11 is actively deprecating VBScript. Applications that still invoke VBScript during startup — as BlueStacks does — will increasingly fail in future Windows 11 builds. A technician who recognises this event and flags it is not just fixing today's ticket, they are preventing a future escalation.

5. **Disabling startup items is reversible and non-destructive.** This is worth communicating to users who are worried about losing access to their applications. Disabled startup items still run when launched manually. The only change is that they no longer consume boot-time resources automatically.

---

## Real World Relevance

Startup performance investigation is a daily task in any environment where users work on managed Windows machines. The combination of Task Manager startup analysis and Event Viewer review represents the entry-level diagnostic toolkit that every help desk technician needs to be fluent with.

The specific scenario in this lab — BlueStacksServices.exe generating VBScriptDeprecationAlert warnings during winlogon — is also directly relevant to real-world fleet management. Android emulators like BlueStacks are frequently installed by users on personal machines that are also used for work, or on shared machines in environments without strict application control. They run significant background services that consume memory and CPU at startup without the user being aware of it, and they use scripting methods that Windows 11 is actively moving away from.

A technician who can open Event Viewer, read a ProcessTree, identify a deprecated scripting invocation during winlogon, connect it to a startup item in Task Manager, and document the finding with an Event ID and timestamp is demonstrating diagnostic fluency that goes beyond checkbox troubleshooting. This is the level of documentation and reasoning that senior technicians and hiring managers look for in IT support candidates.

---

## Troubleshooting Reference

| Symptom | Likely Cause | Resolution |
|---|---|---|
| PC takes more than 60 seconds to reach usable desktop | High-impact startup items loading at boot | Task Manager → Startup Apps → Disable non-essential High items |
| Application crashes immediately at login | Process invoked during winlogon failing | Event Viewer → Application log → filter by Warning/Error around login time |
| Event ID 4096 — VBScriptDeprecationAlert | Application using deprecated VBScript during startup | Identify process in ProcessTree → disable startup item → notify vendor |
| Startup Apps shows process with 100+ children | Background service spawning worker threads | Investigate process → disable if non-essential → monitor for recurrence |
| Last BIOS time above 15 seconds | Slow firmware initialisation | Check BIOS fast boot setting; review connected peripherals at POST |
| Event Viewer Application log shows 17,000+ events | Normal accumulation over time - not itself a fault | Filter by level Warning/Error and time range to find relevant events |
| Disabling startup item does not reduce boot time | Multiple other high-impact items still running | Review full startup list; disable all non-essential High/Medium items |
