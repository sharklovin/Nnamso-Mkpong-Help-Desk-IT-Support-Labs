# Help Desk Ticket #0091

**Category:** Windows 11, BitLocker Recovery, Device Encryption
**Priority:** P1 Critical (user completely locked out, no workaround)
**Status:** Resolved

---

## Ticket Details

| Field | Value |
|---|---|
| Ticket ID | TKT-0091 |
| Date Opened | April 5, 2026 - 09:32 |
| Date Resolved | April 5, 2026 - 10:07 |
| Total Resolution Time | 35 minutes |
| SLA Response Target | 15 minutes (P1 Critical) |
| SLA Resolve Target | 4 hours (P1 Critical) |
| First Response Sent | 09:35 (3 minutes after intake) |
| SLA Response Status | Met |
| SLA Resolve Status | Met |
| Assigned To | Nnamso Mkpong - IT Support L1 |
| Affected User | Chiamaka Bello |
| Department | Legal |
| Machine | WIN11-CB-01 |
| Asset Tag | AST-2026-0147 |
| Location | Remote - working from home |
| Impact | Single user - completely locked out of device |
| Urgency | Critical - no workaround, all data inaccessible |

---

## Issue Reported

User reports that her laptop is displaying a blue recovery screen asking for a 48-digit recovery key. The screen appeared when she powered on this morning. She was not aware of any changes to the device and states the laptop was working normally when she shut it down yesterday evening.

The screen displays the message: Enter the recovery key for this drive. The Key ID shown on screen begins with the first 8 characters of the identifier. User cannot access Windows, cannot access any company data, and cannot start her working day.

---

## Priority Justification

P1 Critical assigned because the user has no workaround. All company data on the device is inaccessible. The user works in Legal and has time-sensitive casework. Every minute of delay has direct business impact. Standard P1 SLA applies: 15-minute response, 4-hour resolution.

---

## Security Protocol Completed

**Identity verification was completed before any recovery key information was accessed or discussed.**

Identity verification steps performed in order:

Step one: User confirmed employee ID EMP-0221.

Step two: User confirmed asset tag AST-2026-0147 visible on the underside of the laptop.

Step three: User confirmed line manager name: Sandra Eze, Finance Manager.

Step four: Cross-referenced asset tag AST-2026-0147 in the asset register - confirmed assigned to Chiamaka Bello, Legal Department, since March 14, 2026.

Identity verification: Confirmed. Proceeding to key retrieval.

> **Note:** Identity was verified before the Key ID on the user's screen was discussed or recorded in this ticket. This is in accordance with the organisational BitLocker recovery security protocol.

---

## Investigation

**09:35 - Initial assessment**

Confirmed with user that the recovery screen appeared after powering on this morning with no user-initiated changes. IT team applied a firmware update to the fleet overnight on April 4. WIN11-CB-01 was included in the firmware update batch. A firmware update changes the UEFI measurements that the TPM chip uses to validate the boot environment. When these measurements change, BitLocker cannot confirm the boot environment is unmodified and triggers a recovery event as a security precaution. This is expected behaviour following a firmware update.

Root cause identified at intake: Firmware update applied overnight (April 4 maintenance window) changed TPM boot measurements, triggering BitLocker recovery on next boot.

**09:38 - Recovery key retrieval**

Navigated to Microsoft Endpoint Manager (Intune) > Devices > All Devices. Searched for WIN11-CB-01. Located device record. Navigated to the Recovery Keys tab. Located one BitLocker recovery key associated with the C: drive. Confirmed the Key ID shown in the admin portal matches the first 8 characters of the Key ID on the user's screen.

Key confirmed: Key ID match verified. Proceeding to key delivery.

> **Security note:** The 48-digit recovery key is not recorded in this ticket. It was retrieved from the approved Intune key management system and read verbally to the user via the support call. It was not sent via email, chat, or any written channel.

---

## Actions Taken

**09:42 - Guided key entry**

Read the 48-digit recovery key to the user in 8 groups of 6 digits, matching the format displayed on the recovery screen. Instructed the user to enter each group and confirm before moving to the next. User confirmed each group was entered correctly.

**09:48 - Key accepted by BitLocker**

User confirmed the recovery screen accepted the key and Windows began loading. Asked user to wait for the desktop to appear fully before confirming resolution.

**09:52 - Access confirmed**

User confirmed Windows loaded to the desktop without further errors. Confirmed she can open files and access her legal case management application.

**09:54 - TPM platform validation update**

To prevent the recovery event from recurring on the next reboot, the TPM platform validation profile must be updated to reflect the new firmware measurements.

Instructed user to open an elevated command prompt and run the following command to suspend BitLocker, allow one reboot, and then automatically resume — resetting the TPM measurements to the new firmware state.

Command used: manage-bde -protectors -disable C:

Instructed user to reboot the machine. Confirmed that BitLocker resumed automatically on the next boot without triggering a recovery screen.

**10:07 — Resolution confirmed after reboot**

User confirmed the machine rebooted normally without a recovery screen. BitLocker is active and the TPM is now validating against the updated firmware measurements. The recovery event will not recur unless another firmware change occurs.

---

## Post-Resolution Advisory Given to User

User was informed of the following:

The recovery event was caused by an overnight firmware update applied by the IT team. It was not caused by anything she did. This is an expected security behaviour and does not indicate a problem with her device or data. All her data remains intact and fully encrypted.

She was advised that if the recovery screen appears again on the next boot to contact the service desk before attempting to enter a key, as a second recovery event before the TPM update is confirmed may indicate a different underlying issue.

She was advised not to write down the recovery key that was used today. The key has been used and will no longer be valid for recovery purposes. A new key will be generated by BitLocker and stored automatically in Intune.

---

## Post-Incident Action Required

The overnight firmware update batch that included WIN11-CB-01 likely included multiple devices. Other users may contact the service desk this morning with the same recovery screen. The following action was taken:

Notified the IT operations team at 10:10 that the firmware update batch triggered BitLocker recovery events. Recommended that the team identify all devices included in the April 4 update batch and proactively apply the TPM validation reset (manage-bde -protectors -disable C: followed by a reboot) to any devices that have not yet been rebooted, to prevent further recovery events.

This converts a reactive ticket-by-ticket response into a proactive fleet-wide resolution.

---

## Root Cause

UEFI firmware update applied during the overnight maintenance window (April 4, 2026) changed the TPM boot measurement profile on WIN11-CB-01. BitLocker detected the change and triggered a recovery event on the next boot as a security precaution. This is expected and correct behaviour — it is BitLocker working as designed. The root cause is an incomplete post-firmware-update procedure: TPM platform validation should be reset on all affected devices after a firmware update as part of the standard change completion checklist.

---

## Outcome

BitLocker recovery completed successfully for WIN11-CB-01. User Chiamaka Bello confirmed access restored at 09:52. TPM platform validation reset performed at 09:54. Reboot confirmed recovery screen did not reappear at 10:07. No data loss. No key exposure. Identity verification completed before key retrieval. Recovery key not recorded in this ticket.

Post-incident advisory sent to IT operations team at 10:10 to proactively address other devices in the firmware update batch.

---

## Measurable Outcome

| Metric | Value |
|---|---|
| Time from call to access restored | 20 minutes |
| Time from call to full closure (TPM reset confirmed) | 35 minutes |
| P1 SLA response target | 15 minutes — Met (3 minutes) |
| P1 SLA resolve target | 4 hours — Met (35 minutes) |
| Data loss | None |
| Security protocol breach | None |
| Identity verification | Completed |
| Recovery key exposure | None — verbal delivery only, not recorded |

---

## Validation Evidence

| Screenshot | What It Confirms |
|---|---|
| `01_bitlocker_off_baseline_annotated.png` | BitLocker off state before encryption — unencrypted baseline |
| `02_recovery_key_backup_options_annotated.png` | Recovery key backup options — Save to file selected |
| `03_encryption_in_progress_annotated.png` | Encryption running at 66.3% — drive protection active from this point |
| `04_encryption_complete_annotated.png` | Encryption of C: complete — drive fully protected |
| `05_recovery_key_saved_location_annotated.png` | Key file saved to external location — filename redacted |
| `06_bitlocker_on_confirmed_annotated.png` | BitLocker on confirmed — management options visible |

---

## Troubleshooting Logic

| Check | Location | Result |
|---|---|---|
| Recovery screen cause | IT change log | Firmware update applied April 4 — root cause confirmed |
| Identity verification | Employee ID, asset tag, manager name | All three confirmed — proceeding authorised |
| Key retrieval source | Microsoft Endpoint Manager > Devices > WIN11-CB-01 > Recovery Keys | Key ID matched to on-screen ID |
| Key entry | User guided verbally | Key accepted, Windows loaded |
| TPM reset | manage-bde -protectors -disable C: followed by reboot | BitLocker resumed, recovery screen did not reappear |
| Root cause | Post-firmware TPM validation not reset | Confirmed — advisory sent to IT operations |
| Resolution | Identity verified, key retrieved, access restored, TPM reset | Confirmed by user at 10:07 |

---

## Security Handling Record

This ticket involved the retrieval and use of a BitLocker recovery key. The following security handling record confirms the protocol was followed correctly.

Identity verified before key access: Yes.
Key retrieved from approved system (Intune): Yes.
Key ID on screen matched Key ID in admin system: Yes.
Key delivered verbally only: Yes.
Key recorded in ticket, email, or chat: No.
Key recorded anywhere outside Intune: No.
User advised not to retain or record the used key: Yes.
Post-recovery TPM reset performed: Yes.

---

## Ticket Closure Note

P1 BitLocker recovery resolved for WIN11-CB-01 — Chiamaka Bello, Legal. Root cause: firmware update applied April 4 changed TPM boot measurements. Recovery key retrieved from Intune after identity verification. Key delivered verbally. Access restored at 09:52. TPM platform validation reset at 09:54. Reboot confirmed at 10:07 — no recurrence. No data loss. Security protocol followed in full. Post-incident advisory sent to IT operations for fleet-wide TPM reset on affected devices. Ticket closed.

**Documented by:**
Nnamso Mkpong
IT Support Portfolio
