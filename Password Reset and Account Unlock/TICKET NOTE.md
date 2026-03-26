#  Help Desk Ticket — #0042

**Category:** Account Management - Lockout & Password Reset  
**Priority:** HIGH  
**SLA:** 1 Hour Response  
**Status:** RESOLVED  

---

## Ticket Details

| Field | Value |
|---|---|
| **Ticket ID** | #0042 |
| **Date Opened** | March 2026 - 09:07 AM |
| **Date Resolved** | March 2026 - 09:24 AM |
| **Resolution Time** | ~17 minutes |
| **Assigned To** | Nnamso Mkpong - IT Support L1 |
| **Affected User** | Ben Tenison |
| **Logon Name** | ben10@mylab.local |
| **Department** | Sales |
| **Device** | Windows 11 workstation - domain-joined |

---

## Issue Reported

User contacted the help desk at 09:07 AM reporting they were unable to log in to their workstation. The login screen was displaying an error message stating the account was locked out. The user communicated urgency as they had a scheduled meeting starting at 09:30 AM.

---

## Checks Performed

1. Confirmed user identity and domain account name (`ben10@mylab.local`).
2. Opened **Active Directory Users and Computers** on the Domain Controller.
3. Navigated to `mylab.local > Users`, located **Ben Tenison** in the user list.
4. Right-clicked the account and opened **Properties > Account tab**.
5. Confirmed the account was locked: the "Unlock account. This account is currently locked out on this Active Directory Domain Controller." message was present.
6. Reviewed the Account Lockout Policy in Group Policy to confirm lockout was the result of 3 failed logon attempts (policy-defined threshold), ruling out a security incident.
7. No anomalous login patterns or off-hours activity detected — confirmed as standard user error.

---

## Action Taken

1. Right-clicked user **Ben Tenison** in ADUC and selected **Reset Password...**.
2. Set a secure temporary password.
3. Enabled:  **"User must change password at next logon"**
4. Enabled:  **"Unlock the user's account"**
5. Clicked **OK** — AD confirmed Account Lockout Status: **Unlocked**.
6. Communicated temporary password securely to user.
7. Guided user through the sign-in and prompted password change process.

---

## Outcome

- User authenticated successfully using the temporary password.
- Windows prompted the user to set a new permanent password before continuing.
- User set a new password that met domain complexity requirements.
- Active Directory Domain Services confirmed: *"The password for Ben Tenison has been changed."*
- User logged in and confirmed access was restored before their 09:30 AM meeting.
- **Ticket resolved within SLA. No escalation required.**

---

## Follow-Up Notes

- No security concern identified. Lockout was the result of forgotten password after a weekend.
- User was advised on best practices: avoid reusing passwords, use a password manager if available.
- No policy changes recommended; current lockout threshold of 3 is appropriate for the environment.
- Ticket closed.

---

*Documented by Nnamso Mkpong | IT Support Portfolio*
