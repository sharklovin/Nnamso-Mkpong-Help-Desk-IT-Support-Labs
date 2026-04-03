# Disk Cleanup and Low Storage Remediation

> **Author:** Nnamso Mkpong
>
> **Domain:** Windows 11 - Storage Management and Disk Cleanup
>
> **Environment:** Windows 11 Client, File Explorer, Settings Storage, Disk Cleanup, Recycle Bin
>
> **Completed:** April 2026

---

## Objective

Investigate and resolve a low disk space complaint on a Windows 11 workstation where both Outlook and Windows Update are failing due to an insufficient free space condition. Record pre-cleanup and post-cleanup storage values, use Disk Cleanup and Recycle Bin to recover space safely, and document the outcome as a structured incident report with measurable results.

---

## Business Scenario

> **Ticket #0078 | Low Disk Space - Outlook Sending Failures and Windows Update Blocked**
>
> A user reports that Microsoft Outlook keeps failing when trying to send and receive email and that Windows Update has not completed successfully in several weeks. Both failures are being caused by the same underlying condition: the system drive is critically low on free space. The user has not made any deliberate changes to the machine and does not know why the drive is full. IT support has been asked to investigate the storage breakdown, recover space safely without removing any user data or business applications and confirm the system returns to a healthy state.

Low disk space is one of the most impactful and preventable faults in a managed Windows environment. Outlook requires free space to maintain its cache, process attachments, and write temporary files during send and receive operations. Windows Update requires free space to download update packages, extract them, and apply them. When the system drive falls below approximately 4 GB of free space both of these processes begin to fail silently from the user's perspective, generating errors they cannot diagnose themselves. The root cause is almost always a combination of accumulated temporary files, a full Recycle Bin, and installer cache growth over time.

---

## Environment and Tools Used

| Component | Detail |
|---|---|
| **Client OS** | Windows 11 |
| **Account** | Nnamso Mkpong (nnamsotechie@gmail.com) |
| **System Drive** | Local Disk C: - 63.0 GB total capacity |
| **Pre-Cleanup Free Space** | 4.08 GB (4.09 GB shown in File Explorer) |
| **Post-Cleanup Free Space** | 34.5 GB |
| **Space Recovered** | approximately 30.4 GB |
| **Tools Used** | File Explorer, Settings → System → Storage, Disk Cleanup, Recycle Bin |

---

## Steps Performed

---

### Phase 1 - Record the Pre-Cleanup Baseline

**Step 1.1 - Check Free Space in File Explorer**

Open File Explorer and navigate to This PC. Observe the Local Disk C: tile. The free space value and the colour of the storage bar provide an immediate indication of severity. A red bar indicates Windows has flagged the drive as critically low.

<img width="553" height="383" alt="01 Local disk low space" src="https://github.com/user-attachments/assets/92a3bbe1-91c5-456a-a3e4-1d1b2fb7708a" />


Pre-cleanup values recorded:

> Drive - Local Disk C:
> Total capacity - 63.0 GB
> Free space - 4.09
> Bar colour Red - Windows critical low space warning

> **Highlighted:** The red progress bar is Windows communicating that the drive is below its critical threshold. At this state, Outlook and Windows Update do not have enough room to operate.

---

**Step 1.2 - Review Storage Breakdown in Settings**

Navigate to **Settings → System → Storage** to see a full breakdown of what is consuming space on the drive. This view gives category-level detail that File Explorer does not provide and it identifies the largest target for cleanup without requiring manual investigation.

<img width="1364" height="720" alt="02 storage settings" src="https://github.com/user-attachments/assets/ef9fc662-e038-4212-a390-7628630e82e6" />


Storage breakdown at pre-cleanup baseline:

**Temporary files** - **30.0 GB** (Largest single category — primary cleanup target)
Installed apps - 8.35 GB  (Review for unused applications)
Other people - 1.97 GB (Other user profiles — do not remove without authorisation)
Documents - 1.00 GB (User documents — do not remove)
Total used - 58.9 GB (Out of 63.0 GB)
**Free space** - **4.08 GB** (Critical threshold — Outlook and Windows Update failing)

> **Highlighted:** The red storage bar at the top confirms 58.9 GB consumed out of 63.0 GB total with only 4.08 GB remaining. Temporary files at 30.0 GB is the dominant category and the safest target for cleanup — Windows regenerates them as needed so removing them does not affect any user data or application. Storage Sense is shown as On at the bottom of the screen. Despite being active it has not prevented the buildup, which indicates the accumulation was rapid or the Storage Sense schedule had not yet triggered.

---

### Phase 2 - Run Disk Cleanup

**Step 2.1 - Launch Disk Cleanup**

Open Disk Cleanup by pressing **Windows + R**, typing `cleanmgr` and pressing Enter. Select drive C: if prompted. Allow the tool to calculate the files available for removal.

<img width="369" height="451" alt="03 Disk clean up" src="https://github.com/user-attachments/assets/21d18fc2-0f86-4fc5-9146-709a52adba98" />

> The Disk Cleanup tool is showing a relatively small amount in this run (23.0 MB) because the primary 30 GB of temporary files identified in Settings Storage was largely cleared through a different path earlier in the cleanup process. Disk Cleanup supplements rather than replaces the Settings Storage cleanup for large temporary file accumulations.

> **Highlighted:** Recycle Bin (5.20 MB) and Thumbnails (11.0 MB) are both checked. The Thumbnails entry is always safe to remove - Windows rebuilds thumbnail previews automatically the next time a folder containing images is opened. This does not affect any user files.

---

### Phase 3 - Empty the Recycle Bin

**Step 3.1 - Confirm Permanent Deletion**

When Recycle Bin is selected in Disk Cleanup and OK is clicked, Windows presents a confirmation dialog before permanently deleting the contents. Read the prompt before confirming.

<img width="482" height="110" alt="04 empty recycle bin" src="https://github.com/user-attachments/assets/da0f1fa7-5d7f-43e6-afcc-165ca0e7561e" />

> Once Yes is clicked, the files cannot be recovered through normal Windows means.

---

### Phase 4 - Verify the Post-Cleanup State

**Step 4.1 - Recheck Free Space in File Explorer**

After the cleanup operations are complete, return to File Explorer → This PC and observe the updated free space value on Local Disk C:.

<img width="691" height="328" alt="05 storage after cleaned" src="https://github.com/user-attachments/assets/f1fc5ce4-3028-4ca2-8a68-86cf5d04be17" />

Post-cleanup values recorded:

| Item | Before Cleanup | After Cleanup | Change |
|---|---|---|---|
| Free space on C: | 4.09 GB | **34.5 GB** | **+30.4 GB recovered** |
| Space used | 58.9 GB | approximately 28.5 GB | 30.4 GB removed |
| Storage bar colour | Red (critical) | Blue (healthy) | Warning resolved |

> **Highlighted:** The free space on Local Disk C: has increased from 4.09 GB to 34.5 GB to a recovery of approximately 30.4 GB. The storage bar has changed from red to the standard fill colour, confirming Windows no longer considers the drive to be in a critical low space state. Outlook and Windows Update now have sufficient free space to operate normally.

---

## Business Impact Note

> **Low disk space failures affect multiple systems simultaneously and the user cannot diagnose the connection between a full drive and their application errors.**

A user experiencing Outlook send and receive failures will typically raise a ticket against Outlook. A user experiencing Windows Update failures will raise a different ticket against Windows Update. Without a storage audit, a technician might investigate both faults as separate application problems and spend significant time on each without finding the cause. Both tickets resolve when the drive is cleaned — but only if the technician checks storage first.

The 30.4 GB recovered in this lab represents the accumulation of temporary files, cached update packages, and Recycle Bin contents that built up over time on a 63 GB drive. On a managed fleet, this category of issue is entirely preventable. Storage Sense configured with an appropriate schedule, a monitored low disk space alert via endpoint management, and a regular cleanup policy would prevent this fault from ever reaching the user's awareness.

For a help desk technician, the ability to look at two seemingly unrelated application failures, identify a shared storage root cause, and resolve both tickets with a single cleanup operation demonstrates diagnostic thinking that goes beyond following a checklist.

---

## Help Desk Ticket Note

See `TICKET-0078-disk-cleanup.md` in this folder.

---

## Outcome and Validation

| Check | Result |
|---|---|
| Pre-cleanup free space recorded: 4.09 GB free of 63.0 GB | Pass |
| Storage Settings breakdown reviewed: 30.0 GB in temporary files | Pass |
| Disk Cleanup run: Recycle Bin and Thumbnails selected | Pass |
| Total space freed by Disk Cleanup: 23.0 MB | Pass |
| Recycle Bin permanent deletion confirmed via dialog | Pass |
| Post-cleanup free space recorded: 34.5 GB free of 63.0 GB | Pass |
| Space recovered: approximately 30.4 GB | Pass |
| Storage bar changed from red to healthy state | Pass |
| Outlook and Windows Update expected to resume normal operation | Pass |

---

## What I Learned

1. **Free space below 5 GB on a Windows 11 system drive causes multiple simultaneous application failures.** Outlook, Windows Update, and any application that writes temporary files to the system drive will begin to fail. These failures look unrelated until a storage audit reveals the shared root cause. Checking free space early in any investigation saves time.

2. **Settings → System → Storage is more useful than File Explorer for identifying the cause.** File Explorer shows total free space but not what is consuming it. The Storage breakdown categorises usage by temporary files, installed apps, documents, and other categories, which tells the technician exactly where to focus the cleanup.

3. **Temporary files are always the safest and largest cleanup target.** In this lab they accounted for 30.0 GB out of 58.9 GB of used space — more than half the drive. Temporary files are created by Windows and applications during normal operation and are designed to be deleted. Removing them does not affect user data, installed applications, or system function.

4. **Disk Cleanup and Storage Settings cleanup are complementary, not interchangeable.** Disk Cleanup handles specific file types including Recycle Bin and thumbnail cache. Storage Settings cleanup handles the broader temporary files category. Running both ensures complete coverage. In this lab the large recovery came from the Settings cleanup path and Disk Cleanup provided the final pass for smaller items.

5. **Storage Sense being enabled does not guarantee prevention.** Storage Sense was shown as On in this lab, yet the drive reached a critical 4.08 GB free state. This is because Storage Sense runs on a schedule and has a minimum free space threshold before it triggers aggressively. In environments where storage accumulates rapidly, a manually configured Storage Sense schedule or a proactive monitoring policy is needed to catch the problem before it reaches the application failure stage.

---

## Real World Relevance

Disk space remediation is one of the most common and most preventable help desk tasks in any Windows environment. It arrives as Outlook errors, Windows Update failures, application crashes, and general sluggishness - rarely as a direct report of "my disk is full" because most users do not know how to check.

The technician workflow in this lab  checks free space, break down usage by category, identify the largest safe cleanup target, run the appropriate tools, recheck and record the result - is repeatable on any Windows 11 machine and takes under 10 minutes to complete. It resolves multiple simultaneous complaints with a single set of actions, which is the highest-value outcome in help desk work.

The numbers matter here in a way they do not in most other support tasks. Recording 4.09 GB free before cleanup and 34.5 GB free after cleanup gives a hiring manager or senior technician an immediate sense of scale and confirms the job was done properly. Support portfolios that include measurable before and after values are consistently more credible than those that describe what was done without recording what changed.

---

## Troubleshooting Reference

| Symptom | Likely Cause | Resolution |
|---|---|---|
| Outlook send and receive failures with no network error | System drive below free space threshold | Check C: free space via File Explorer or Storage Settings |
| Windows Update downloads fail or partially complete | Insufficient space for update package extraction | Free at minimum 10 GB on system drive before retrying update |
| Red storage bar on C: in File Explorer | Drive below Windows critical threshold (approximately 5 GB) | Run Storage Settings cleanup followed by Disk Cleanup |
| Temporary files category shows more than 10 GB in Storage Settings | Normal accumulation not yet cleared by Storage Sense | Settings → Temporary files → Remove files |
| Disk Cleanup shows less than 1 GB to recover despite low space | Large files are in user data, installed apps, or other profile data | Audit installed apps and Downloads folder with user present |
| Storage Sense is On but drive still reached critical state | Schedule interval too long or threshold not triggered | Configure Storage Sense to run every 14 days or enable low disk trigger |
| Free space recovers but returns low within days | Ongoing large temporary file generation or user downloads | Investigate which application is generating large temp files |
