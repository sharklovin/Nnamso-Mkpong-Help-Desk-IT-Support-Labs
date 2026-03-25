# Lab 1: Password Reset and Account Unlock

> **Domain:** Active Directory Administration |
> **Environment:** Windows Server 2022 + Windows 11 Client (Virtualised)  
> **Completed:** March 2026

---

## Table of Contents

- [Objective](#objective)
- [Business Scenario](#business-scenario)
- [Environment & Tools Used](#environment--tools-used)
- [Lab Architecture](#lab-architecture)
- [Steps Performed](#steps-performed)
  - [Phase 1 — Create the Test User](#phase-1--create-the-test-user-in-active-directory)
  - [Phase 2 — Configure Account Lockout Policy via GPO](#phase-2--configure-account-lockout-policy-via-gpo)
  - [Phase 3 — Trigger the Account Lockout](#phase-3--trigger-the-account-lockout-on-the-client)
  - [Phase 4 — Investigate & Resolve on the Domain Controller](#phase-4--investigate--resolve-on-the-domain-controller)
  - [Phase 5 — Validate the Fix on the Client](#phase-5--validate-the-fix-on-the-client)
- [Help Desk Ticket Note](#help-desk-ticket-note)
- [Outcome & Validation](#outcome--validation)
- [What I Learned](#what-i-learned)
- [Real World Relevance](#real-world-relevance)
- [Troubleshooting Reference](#troubleshooting-reference)

---

## Objective

Reset a forgotten password for a domain user account, unlock a locked-out Active Directory account and enforce a mandatory password change at the user's next sign-in. All within an Active Directory Domain Services (AD DS) environment.

---

## Business Scenario

> **Ticket #0042 | Priority: HIGH | Reported: 09:05 AM**
>
> A user (**Ben Tenison**, logon: `ben10@mylab.local`) reports they cannot log in to their workstation ahead of a critical 9:30 AM meeting. Multiple failed password attempts have triggered an account lockout. The user needs access restored immediately.

This scenario mirrors one of the most common and time-sensitive issues a help desk technician faces daily.

---

## Environment & Tools Used

| Component | Details |
|---|---|
| **Domain Controller OS** | Windows Server 2022 Evaluation |
| **Client OS** | Windows 11 |
| **Domain** | `mylab.local` |
| **Tool: ADUC** | Active Directory Users and Computers |
| **Tool: GPMC** | Group Policy Management Console |
| **Tool: CMD** | Command Prompt (Administrator) - `gpupdate /force` |
| **Deployment** | Hyper-V / VirtualBox (local lab) |

---

## Lab Architecture

```
┌─────────────────────────────────────────────────────────┐
│                      mylab.local                        │
│                                                         │
│   ┌──────────────────────┐     ┌──────────────────────┐ │
│   │  Windows Server 2022 │     │   Windows 11 Client  │ │
│   │  Domain Controller   │◄────│   (ben10@mylab.local)│ │
│   │                      │     │                      │ │
│   │  • AD DS             │     │  • Joined to domain  │ │
│   │  • ADUC              │     │  • Test login point  │ │
│   │  • Group Policy Mgmt │     │                      │ │
│   └──────────────────────┘     └──────────────────────┘ │
└─────────────────────────────────────────────────────────┘
```


---

## Steps Performed

---

### Phase 1: Create the Test User in Active Directory

**Goal:** Create the domain user `ben10` (Ben Tenison) who will be used throughout this lab.

---

**Step 1.1 - Open Active Directory Users and Computers (ADUC)**

Press the `Windows` key, type **Active Directory** and select **Active Directory Users and Computers** from the search results.

<img width="760" height="642" alt="1" src="https://github.com/user-attachments/assets/f5c42b3f-e1e4-451c-a471-d598dbb6c403" />


>  **Note:** ADUC is the primary GUI tool for managing domain user accounts, groups and OUs. All user administration in this lab takes place here.

---

**Step 1.2 - Browse the Domain Structure**

Expand the `mylab.local` domain node in the left panel to see the default containers: Builtin, Computers, Domain Controllers, Users, etc.

<img width="754" height="530" alt="2" src="https://github.com/user-attachments/assets/60c0751c-94fb-4996-9caa-7676bff278af" />


---

**Step 1.3 - Create a New User**

Right-click the **Users** container → **New** → **User**.

<img width="990" height="614" alt="3" src="https://github.com/user-attachments/assets/c91c5e4f-2130-46f7-ac9f-4a810d7f8934" />


---

**Step 1.4 - Fill in User Details**

Enter the following details in the New Object - User wizard:

| Field | Value |
|---|---|
| First name | `Ben` |
| Last name | `Tenison` |
| User logon name | `ben10` |
| Domain | `@mylab.local` |

Click **Next**.

<img width="444" height="400" alt="4" src="https://github.com/user-attachments/assets/51aa1870-50f7-4858-9a61-c4db7f758e78" />


---

**Step 1.5 - Set Initial Password**

Set an initial password. Leave **"User must change password at next logon"** unchecked for now — we will enforce this later via the Reset Password dialog after the lockout.

Click **Next**.

<img width="434" height="376" alt="5" src="https://github.com/user-attachments/assets/1a27e1fa-7284-4a3b-8c33-c5eb6c9cd864" />


---

**Step 1.6 - Confirm and Finish**

Review the summary screen. Confirm the user `ben10@mylab.local` will be created and click **Finish**.

<img width="439" height="379" alt="6" src="https://github.com/user-attachments/assets/4f6f8e27-32ef-494d-83a5-f2bd3dfe4090" />


 **User `ben10` (Ben Tenison) has been created in the `mylab.local` domain.**

---

### Phase 2: Configure Account Lockout Policy via GPO

**Goal:** Set a Group Policy that locks accounts after 3 failed login attempts, so we can reliably trigger a lockout for the lab scenario.

---

**Step 2.1 - Open Group Policy Management**

Press `Windows`, type **Group Policy Management** and open it.

<img width="756" height="640" alt="GPO1" src="https://github.com/user-attachments/assets/0438059c-ce9e-4daf-adcc-65d7a4fcd3a6" />


---

**Step 2.2 - Navigate to Account Lockout Policy**

In the Group Policy Management console, navigate to:

```
Forest: mylab.local
  └── Domains
        └── mylab.local
              └── Default Domain Policy  [Right-click → Edit]
```

Then in the Group Policy Management Editor:

```
Computer Configuration
  └── Policies
        └── Windows Settings
              └── Security Settings
                    └── Account Policies
                          └── Account Lockout Policy
```

The final configured policy values are shown below:

<img width="1021" height="727" alt="GPO FINALE 1" src="https://github.com/user-attachments/assets/50985313-f2ee-40f1-9d5c-198738b3a1a6" /> 

---

<img width="1022" height="726" alt="GPO FINALE2" src="https://github.com/user-attachments/assets/1f79cb74-ed80-474b-8eb1-315ed14a1d80" />



---

**Step 2.3 - Set Account Lockout Threshold**

Double-click **Account lockout threshold** and set it to **3 invalid logon attempts**.

<img width="406" height="496" alt="GPO THRESHOLD" src="https://github.com/user-attachments/assets/69a5c1da-3d96-4354-b05d-9769813fb759" />


>  **Why 3?** In a real environment, 5–10 attempts is more user-friendly. We use 3 here to make the lab quick and repeatable.

---

**Step 2.4 - Set Account Lockout Duration**

Set the lockout duration to **15 minutes**. This means a locked account auto-unlocks after 15 minutes without admin intervention (useful for lower-priority lockouts).

<img width="419" height="502" alt="GPO LOCK OUT DURATION" src="https://github.com/user-attachments/assets/681c75e4-fa80-40ac-94ad-f1f22d0d8cc9" />


---

**Step 2.5 - Set Reset Account Lockout Counter After**

Set the counter reset to **15 minutes**. This resets the failed attempt count if no further failures occur within the window.

<img width="419" height="503" alt="GPO ACCOUNT LOCK OUT COUNTER" src="https://github.com/user-attachments/assets/13591f55-7ae0-4ef7-80ae-8eaaac6abdd2" />


---

**Step 2.6 - Force Group Policy Update via CMD**

Open **Command Prompt** as Administrator.

<img width="760" height="639" alt="CMD OPEN" src="https://github.com/user-attachments/assets/2667063b-3139-4efb-a8e1-bf6f59fd6779" />


Run the following command to push the new policy to the domain immediately without waiting for the standard refresh cycle:

```cmd
gpupdate /force
```

<img width="976" height="300" alt="CMD COMMAND" src="https://github.com/user-attachments/assets/9ad1aa97-36f0-49fd-909d-a1e6acbcb455" />


The command output confirms the policy refresh is processing:

<img width="1015" height="731" alt="CMD UPDATE" src="https://github.com/user-attachments/assets/6193480c-5b30-4236-bd2d-989245db20ab" />


 **Account Lockout Policy is now active across the domain.**

---

### Phase 3 - Trigger the Account Lockout on the Client

**Goal:** Reproduce the business scenario by entering the wrong password on the Windows 11 client until the account locks.

---

**Step 3.1 - Attempt Login with Wrong Password (x3)**

On the Windows 11 client, attempt to sign in as `ben10@mylab.local` using an **incorrect password** three times in a row.

After the 3rd failed attempt, Windows displays the lockout message:

<img width="1321" height="755" alt="LOCKED ACCOUNT STATE" src="https://github.com/user-attachments/assets/b540b94d-1bfc-43d2-8852-37f916ee13db" />


>  **"The referenced account is currently locked out and may not be logged on to."**  
> This confirms the GPO lockout threshold of 3 has been triggered. This is exactly what the user would see and report to the help desk.

 **Account lockout successfully replicated. The ticket scenario is live.**

---

### Phase 4 - Investigate & Resolve on the Domain Controller

**Goal:** As the help desk/IT admin, locate the affected account, confirm the lock state, reset the password and unlock the account.

---

**Step 4.1 - Locate the User in ADUC**

Back on the Domain Controller, open **ADUC** and navigate to the **Users** container. Locate **Ben Tenison** in the user list.

<img width="848" height="624" alt="VALIDATING OF USER" src="https://github.com/user-attachments/assets/7801232f-069a-4264-b076-8cc9c5ec4b95" />


---

**Step 4.2 - Open User Properties to Verify Lock State**

Right-click **Ben Tenison** → **Properties**.

<img width="851" height="626" alt="checking for account state " src="https://github.com/user-attachments/assets/febbb3fb-12d9-4e9b-a031-0d99c74cd342" />


Navigate to the **Account** tab. You will see the following message:

> *"Unlock account. This account is currently locked out on this Active Directory Domain Controller."*

The **Unlock account** checkbox is visible, confirming the account is locked.

<img width="411" height="537" alt="ACCOUNT LOCK STATE VERIFICATION" src="https://github.com/user-attachments/assets/259a0dbe-b116-4cc4-be1c-b6e2855fe8e8" />



---

**Step 4.3 - Initiate Password Reset**

Close Properties. Right-click **Ben Tenison** again and select **Reset Password...**.

<img width="875" height="627" alt="RESET PASSWORD" src="https://github.com/user-attachments/assets/ebe14731-8520-4e1a-b26c-409f8a8cd8a1" />


---

**Step 4.4:  Set Temporary Password, Unlock Account, and Force Change**

In the Reset Password dialog:

1.  Enter a **temporary password** 
2.  Confirm the temporary password
3.  Check **"User must change password at next logon"**
4.  Check **"Unlock the user's account"**
5. Click **OK**

<img width="380" height="257" alt="RESET PASSWORD DONE" src="https://github.com/user-attachments/assets/5504b08d-7b77-47e3-9120-4bfe70ea6fa2" />


>  **Security Best Practice:** Always use "User must change password at next logon" when resetting passwords. This ensures the temporary password is never kept as the permanent one and protects against insider threat from the admin knowing the final password.

Notice the status line: **"Account Lockout Status on this Domain Controller: Unlocked"** confirming the unlock is applied simultaneously.

 **Password reset and account unlock applied in a single action.**

---

### Phase 5 - Validate the Fix on the Client

**Goal:** Confirm the user can sign in with the temporary password and is prompted to set a new one.

---

**Step 5.1 - Sign In with Temporary Password**

On the Windows 11 client, sign in as `ben10@mylab.local` using the temporary password set in Step 4.4.

Windows immediately prompts the user to change their password before continuing:

<img width="1364" height="581" alt="BEN 1O RESET PASSWORD 1" src="https://github.com/user-attachments/assets/812f8e51-1ce6-49c1-80ee-d5a869d9b465" />

---

<img width="1324" height="631" alt="BEN 1O RESET PASSWORD 1 1" src="https://github.com/user-attachments/assets/da827127-6fb7-4e44-9241-c43adff1c953" />


> *"The user's password must be changed before signing in."*

---

**Step 5.2 - Set a New Permanent Password**

The user enters a new permanent password that meets the domain complexity requirements and confirms it.

<img width="946" height="552" alt="BEN 1O RESET PASSWORD 2" src="https://github.com/user-attachments/assets/9b2f2daa-2104-4a22-a02e-f234fa93b03f" />




---

**Step 5.3 - AD Confirms Password Change**

Active Directory Domain Services displays a final confirmation dialog:

<img width="1358" height="767" alt="BEN 1O RESET PASSWORD 2 1" src="https://github.com/user-attachments/assets/475411a8-7f88-4172-b895-78c65c00c07f" />

> > *"Your password has been changed."*

 **User can now sign in with their new permanent password. Issue fully resolved.**

---

## Help Desk Ticket Note

```
──────────────────────────────────────────────────────────────
TICKET #0042 | ACCOUNT LOCKOUT / PASSWORD RESET
Technician: Nnamso Mkpong | Date: March 2026 | Priority: HIGH
──────────────────────────────────────────────────────────────

ISSUE REPORTED:
  User Ben Tenison (ben10@mylab.local) unable to log in to domain
  workstation. Login screen displaying account lockout error.
  User flagged urgency due to meeting starting at 09:30 AM.

CHECKS PERFORMED:
  1. Opened Active Directory Users and Computers on the DC.
  2. Located user 'Ben Tenison' in the mylab.local > Users container.
  3. Opened user Properties > Account tab — confirmed account was
     locked out on the domain controller.
  4. Verified GPO Account Lockout Policy: threshold set to 3 invalid
     attempts, confirming lockout was policy-triggered (not a
     compromised account incident).

ACTION TAKEN:
  1. Right-clicked user > Reset Password.
  2. Set a temporary password: communicated securely to user.
  3. Enabled: "User must change password at next logon."
  4. Enabled: "Unlock the user's account."
  5. Clicked OK — lockout status confirmed as Unlocked.
  6. Advised user to sign in with temp password and set new one.

OUTCOME:
  User successfully authenticated with temporary password.
  Forced password change prompt appeared as expected.
  User set new permanent password — confirmed by AD DS dialog.
  User logged in successfully before their 09:30 AM meeting.
  Ticket resolved. No further escalation required.

FOLLOW-UP NOTES:
  - No signs of brute-force or malicious activity. Human error only.
  - User advised on importance of password management.
  - No policy changes required; existing lockout thresholds adequate.
──────────────────────────────────────────────────────────────
```

---

## Outcome & Validation

| Validation Check | Result |
|---|---|
| Account lockout confirmed in ADUC |  Pass |
| Password reset via ADUC Reset Password dialog |  Pass |
| Account unlocked simultaneously with reset |  Pass |
| "Must change password at next logon" enforced |  Pass |
| User prompted to change password at sign-in |  Pass |
| New password accepted and confirmed by AD DS |  Pass |
| User able to sign in successfully |  Pass |

---

## What I Learned

1. **Account lockouts are policy-driven** : The `Account Lockout Threshold` GPO setting controls how many failed attempts trigger a lock. Understanding this helps distinguish between a forgotten password and a potential security event.

2. **ADUC is the central tool** : Active Directory Users and Computers is the go-to interface for viewing account state, resetting passwords, unlocking accounts and checking account options all in one place.

3. **Reset Password vs. Properties**: Both paths can unlock an account, but **Reset Password** is the faster, single-action resolution that simultaneously resets the password, unlocks the account and enforces a forced change — making it the correct choice for help desk scenarios.

4. **"User must change password at next logon" is a security best practice**: It ensures the admin never knows the user's final password, protecting both the user and the organisation from credential exposure.

5. **`gpupdate /force` is essential in a lab**: Group Policy changes can take up to 90 minutes to propagate normally. Running `gpupdate /force` on the domain controller ensures the policy takes immediate effect, which is critical for both lab work and urgent real-world deployments.

---

## Real World Relevance

Password resets and account lockouts are consistently among the **top 3 most common help desk tickets** in any organisation using Active Directory. According to industry data, they can account for 20–50% of all Level 1 support volume.

**Why this matters in a real support environment:**

- **Time sensitivity is high.** Users locked out before meetings, presentations or shift starts create immediate business impact. Making speed and accuracy are critical.

- **Security awareness is essential.** Not all lockouts are accidental. A sudden rash of lockouts across accounts can indicate a password spray attack. A technician must be able to tell the difference between a user forgetting their password and a potential security incident.

- **The "User must change password" flag protects everyone.** If an admin resets a password and does not force a change, the admin now knows the user's live credential which leads to an unnecessary security risk. Always enforce the change.

- **Policy knowledge prevents over helpfulness.** Understanding the Account Lockout Duration (15 minutes in this lab) means a technician can make an informed decision: for a non-urgent case, they can inform the user to simply wait for the auto-unlock rather than escalating unnecessarily.

- **Documentation matters.** A well-written ticket note issue, checks, action, outcome protects the technician, satisfies audit requirements and helps the next person who reads the ticket understand exactly what happened.

---

## Troubleshooting Reference

| Symptom | Likely Cause | Resolution |
|---|---|---|
| "Unlock account" checkbox not visible | Account is not locked; it may be disabled | Check if account is disabled instead of right-click → Enable Account |
| GPO not applying after configuration | Policy hasn't propagated yet | Run `gpupdate /force` on the domain controller |
| User not found in Users container | Account may be in a different OU | Use ADUC Find (Ctrl+F) to search the whole domain |
| Lockout happening immediately on login | Clock skew between DC and client | Check time sync — `w32tm /query /status` |
| Password reset dialog greys out "Unlock" | Account not currently locked | No unlock needed; just reset the password |
| New password rejected by domain | Doesn't meet complexity requirements | Remind user: 8+ chars, uppercase, lowercase, number, symbol |

---

*Lab completed and documented by Nnamso Mkpong — IT Support Portfolio | GitHub: Sharklovin*
