# Lab 04: Map Shared Drives and Fix Permissions

> **Author:** Nnamso Mkpong
>
> **Domain:** Active Directory Administration File Sharing and Permissions
>
> **Environment:** Windows Server 2022 + Windows 11 Client (Virtualised)
> 
> **Completed:** March 2026

---

## Objective

Create a shared folder on the server, configure share and NTFS permissions scoped to a security group, map the drive on a client machine and troubleshoot access denied errors by testing with both an authorised and an unauthorised user.

---

## Business Scenario

> **Ticket #0053 | Department Share Access - Permissions Fault**
>
> A user reports they cannot access the department file share after being moved to a new team. A second user on the same team has no issues. IT support has been asked to investigate whether the fault lies in share permissions, NTFS permissions or group membership  and to restore correct access without granting permissions directly to individual accounts.

This scenario covers two of the most frequent permission related support tasks: provisioning a shared folder correctly from scratch and diagnosing why one user can access a share while another cannot.

---

## Environment and Tools Used

| Component | Detail |
|---|---|
| **Server OS** | Windows Server 2022 Evaluation |
| **Client OS** | Windows 11 |
| **Domain** | [DOMAIN] |
| **Domain Controller IP** | [DC_IP] |
| **Shared Folder** | DepartmentShare |
| **Share Path** | \\\\[DC_IP]\\DepartmentShare |
| **Mapped Drive Letter** | Z: |
| **Security Group** | Department_Sales (Global Security) |
| **Authorised User** | Jayson Sule (Jayson419[DPMAIN]) |
| **Test User (unauthorised)** | Otobong Mkpong (Choko101@m[DOMAIN]) |
| **Tools Used** | ADUC, File Explorer, Advanced Sharing, Security tab (NTFS) |

---

## Lab Architecture

```
[DOMAIN]
│
├── Domain Controllers
│     └── WIN-SERVER01 ([DC_IP])
│           └── C:\Users\Administrator\Desktop\DepartmentShare  ← Shared folder
│
└── Users
      ├── Jayson Sule       ← Member of Department_Sales → access allowed
      ├── Otobong Mkpong    ← NOT in Department_Sales → access denied (test)
      └── Department_Sales  ← Security group controlling all access
```

---

## NTFS Permissions vs Share Permissions - Key Concept

> **Understanding the difference between these two permission layers is the most important diagnostic skill in this lab.**

Windows applies **two independent permission layers** to any network share. Both must allow access for a user to reach a file. If either layer denies, the user is blocked.

| Layer | Where it lives | What it controls | Applied via |
|---|---|---|---|
| **Share Permissions** | The share itself | Who can connect over the network | Advanced Sharing → Permissions |
| **NTFS Permissions** | The file system | Who can read, write, or modify files/folders | Properties → Security tab |

**The effective permission is always the most restrictive combination of both layers.**

```
User attempts to open \\[DC_IP]\DepartmentShare
         │
         ▼
Share Permission check
  Is Department_Sales granted Change or Read?
         │ YES              │ NO
         ▼                  ▼
NTFS Permission check   Access Denied immediately
  Does Department_Sales
  have Read & Execute
  or Write on the folder?
    │ YES           │ NO
    ▼               ▼
Access granted   Access Denied
```

In most enterprise environments, share permissions are set to **Change** for the security group and NTFS permissions are used for fine grained control. This is the approach used in this lab.

---

## Steps Performed

---

### Phase 1 - Create the Shared Folder on the Server

**Step 1.1 - Create the DepartmentShare Folder**

On the Window Server 22 machine, navigate to a suitable location and create a new folder named **DepartmentShare**.

<img width="625" height="454" alt="Department Share Folder" src="https://github.com/user-attachments/assets/9816e3b8-cd5a-48c2-95b6-3e15fca2b4af" />


> The folder name becomes the share name that clients use to connect.

---

**Step 1.2 - Enable Advanced Sharing and Set the Share Name**

Right-click the folder → **Properties → Sharing tab → Advanced Sharing**. Tick **Share this folder**. Confirm the share name is **DepartmentShare**. Click **Permissions** before clicking OK.

<img width="351" height="365" alt="Shared Folder tick" src="https://github.com/user-attachments/assets/900283be-83e8-487c-9247-b6932eabd8ee" />


> The share name in the Advanced Sharing dialog is what appears in the UNC path. `\\[DC_IP]\DepartmentShare` is what clients will use to map the drive. Do not change this name after users have mapped it as doing so breaks all existing drive mappings.

---

### Phase 2 - Configure Share Permissions

**Step 2.1 - Add Department_Sales to Share Permissions**

Inside the Permissions dialog, click **Add** and type `Department_Sales`. Click **Check Names** to resolve the group against the directory, then click **OK**.

<img width="363" height="446" alt="03-share-permissions-department-sales" src="https://github.com/user-attachments/assets/6d7a2e66-27c8-4205-bad7-4add4fa02c44" />


---

**Step 2.2 - Grant Change and Read to Department_Sales**

With **Department_Sales** selected in the group list, tick **Allow** for both **Change** and **Read**. Leave **Full Control** unticked. Click **Apply**, then **OK**.

<img width="363" alt="Share permissions showing Department_Sales with Change and Read allowed" src="screenshots/03-share-permissions-department-sales.png" />

> **You may ask why not Full Control on the share?** Full Control at the share level grants users the ability to change permissions on the share itself. This is almost never appropriate for end users. 

---

### Phase 3 - Configure NTFS Permissions

**Step 3.1 - Open the Security Tab and Add Department_Sales**

Back on the folder Properties, click the **Security tab**. Click **Edit → Add**, type `Department_Sales`, click **Check Names**, then **OK**.

<img width="454" height="253" alt="05-adding-group-to-permissions" src="https://github.com/user-attachments/assets/10b73449-74b0-49b4-88aa-28e3a5772c55" />


Grant the following NTFS permissions to Department_Sales:

| Permission | Allow |
|---|---|
| Read & execute | ✅ |
| List folder contents | ✅ |
| Read | ✅ |
| Write | ✅ |
| Full control | ❌ |
| Special permissions | ❌ |

Click **Apply**, then **OK**.

> NTFS permissions are the authoritative access control layer. The combination of Read, Write, and Read & Execute gives members the ability to open, create, and edit files inside the share without being able to change the folder's permission settings.

---

### Phase 4 - Create Users and the Security Group

**Step 4.1 - Create the Department_Sales Security Group**

In ADUC, right-click **Users** → **New → Group**. Set the group name to **Department_Sales**, scope to **Global**, type to **Security**. Click **OK**.

<img width="437" height="378" alt="creating group" src="https://github.com/user-attachments/assets/66af0539-a361-49b9-b55e-3e2b608d035b" />


---

**Step 4.2 — Confirm Group Properties**

Right-click **Department_Sales → Properties** to confirm it was created with Global scope and Security type.


---

**Step 4.3 - Create the First Test User (Jayson Sule — Authorised)**

Right-click the Users container → **New → User**. Fill in the user details:

| Field | Value |
|---|---|
| First name | Jayson |
| Last name | Sule |
| Full name | Jayson Sule |
| User logon name | Jayson419 |
| Domain | @mylab.local |

Click **Next**.

<img width="430" alt="New Object User wizard — Jayson Sule details filled in" src="screenshots/06-creating-user-jayson-sule.png" />

---

**Step 4.4 - Set Password and Options**

Enter and confirm the initial password. Tick **Password never expires** for this lab environment. Click **Next**.

<img width="433" height="376" alt="07-user-password-settings" src="https://github.com/user-attachments/assets/ff560eac-8798-4485-ada9-eaa194f9af98" />




---

**Step 4.5 - Confirm and Finish User Creation**

Review the summary. Confirm the logon name is `Jayson419@[DOMAIN]`. Click **Finish**.


---

**Step 4.6 - Add Jayson Sule to Department_Sales**

Right-click **Jayson Sule → Properties → Member Of tab → Add**. Type `Department_Sales`, click **Check Names**, then **OK**.

Confirm **Department_Sales** now appears in the Member Of list alongside Domain Users.

<img width="407" height="536" alt="User added to department sales" src="https://github.com/user-attachments/assets/dbe951a8-5847-410c-816a-454e4d310d9b" />





---

**Step 4.7 - Create the Second Test User (Otobong Mkpong - Unauthorised)**

Repeat the user creation steps to create **Otobong Mkpong** with logon name `Choko101@[DOMAIN]`. This user is intentionally left out of Department_Sales to simulate an access denied scenario.

<img width="437" height="375" alt="cREATED NEW USER" src="https://github.com/user-attachments/assets/d5b07a26-559b-48e9-9071-91332becc06b" />


---

### Phase 5 - Map the Network Drive on the Client

**Step 5.1 - Open Map Network Drive in File Explorer**

On the Windows 11 client, open **File Explorer → This PC  →  Map network drive** (right-click on This PC to see Map network driver option). Set the following:

| Field | Value |
|---|---|
| Drive letter | Z: |
| Folder | \\\\[DC_IP]\\DepartmentShare |
| Reconnect at sign-in | check |

Click **Finish**.

<img width="610" height="449" alt="mAPPING DRIVE" src="https://github.com/user-attachments/assets/68eaddac-ded0-46da-b6b6-de21b1dbb920" />


---

**Step 5.2 - Confirm the Drive Appears in This PC**

After mapping, open **This PC** and confirm the **DepartmentShare (\\\\192.168.1.10) (Z:)** drive appears under Network locations.

<img width="584" height="306" alt="CONFIRMATION OF MAPPED NETWORKS" src="https://github.com/user-attachments/assets/5b32de7d-4a1b-4a60-b1f6-a485a90d6ddd" />


---

### Phase 6 - Test Access: Authorised User

**Step 6.1 - Log In as Jayson Sule and Write to the Share**

Log into the client as `Jayson419@[DOMAIN]`. Open the Z: drive and create a test file to confirm write access is working.

<img width="786" height="594" alt="cREATED A FOLDER AND IT WORKED" src="https://github.com/user-attachments/assets/402eada4-c276-4616-9e48-5e0ade13d659" />


>  Jayson Sule is a member of Department_Sales. Both share and NTFS permissions allow the group to read and write. The file is created successfully. Access is confirmed.

---

### Phase 7 - Test Access: Unauthorised User (Access Denied Simulation)

**Step 7.1 - Attempt Access as Otobong Mkpong**

Log into the client as `Choko101@[DOMAIN]`. Attempt to open `\\[DC_IP]\Departmentshare\`.

<img width="516" height="194" alt="PERMISION DENIED" src="https://github.com/user-attachments/assets/7dbc0717-5211-424f-b630-f977666d616f" />


>  Otobong Mkpong is not a member of Department_Sales. The share permissions only grant access to that group. Windows returns: **"You do not have permission to access \\[DC_IP]\Departmentshare\"**. This is the expected and correct result.

---

### Phase 8 - Resolve the Access Issue (Reinstate Group Membership)

**Step 8.1 - Add Otobong to Department_Sales**

On the Domain Controller, open ADUC. Right-click **Otobong Mkpong → Properties → Member Of → Add**. Type `Department_Sales`, click **Check Names**, then **OK**.

<img width="405" height="532" alt="ADDED OTONGOBONG TO DEPARTMENT SALES" src="https://github.com/user-attachments/assets/ab3165d8-4702-4c6e-b9fb-9730869c20ee" />


---

**Step 8.2  Confirm Group Membership**

Back on the Member Of tab, **Department_Sales** now appears alongside Domain Users.

> After adding the user to the group, ask them to **log off and log back on** as any user who is added to a group mid-session must restart their session before the new membership is reflected.
---

**Step 8.3 - Verify Access is Restored**

Log back in as Otobong Mkpong and attempt to open the share again. The drive opens successfully and the test file created by Jayson is visible, confirming that group membership was the only missing element.

<img width="786" height="594" alt="cREATED A FOLDER AND IT WORKED" src="https://github.com/user-attachments/assets/ca2c1ded-7e1e-4743-9657-0ca6bb1bfa05" />

---

## Help Desk Ticket Note

```
──────────────────────────────────────────────────────────────────
TICKET #0053 | DEPARTMENT SHARE ACCESS — PERMISSIONS FAULT
Technician: Nnamso Mkpong | Date: March 2026 | Priority: STANDARD
──────────────────────────────────────────────────────────────────

ISSUE REPORTED:
  User Otobong Mkpong (Choko101@mylab.local) unable to access
  \\192.168.1.10\DepartmentShare. Error: "You do not have
  permission to access this network resource." Second user
  Jayson Sule has no issues accessing the same share.

INVESTIGATION:
  1. Confirmed share exists and is accessible on the server.
  2. Checked share permissions: Department_Sales group has
     Change and Read is correctly configured.
  3. Checked NTFS permissions: Department_Sales has Read,
     Write, Read & Execute is correctly configured.
  4. Checked Jayson Sule Member Of: Department_Sales present.
  5. Checked Otobong Mkpong Member Of: Department_Sales ABSENT.
  Root cause confirmed: missing group membership, not a
  permissions misconfiguration on the share or NTFS layer.

ACTIONS TAKEN:
  1. Created DepartmentShare folder on server.
  2. Enabled Advanced Sharing with share name DepartmentShare.
  3. Set share permissions: Department_Sales — Change + Read.
  4. Set NTFS permissions: Department_Sales — Read, Write,
     Read & Execute, List folder contents.
  5. Created user Jayson Sule (Jayson419[DOMAIN]).
  6. Created user Otobong Mkpong (Choko101[DOMAIN]).
  7. Added Jayson Sule to Department_Sales.
  8. Left Otobong out of Department_Sales to reproduce error.
  9. Mapped Z: drive as \\[DC_IP]\DepartmentShare.
  10. Tested with Jayson - access granted, file created. Pass.
  11. Tested with Otobong - access denied. Pass (expected).
  12. Added Otobong to Department_Sales in ADUC.
  13. Asked user to log off and log back on.
  14. Retested with Otobong - access granted. Resolved.

OUTCOME:
  Access restored. Root cause was missing group membership.
  Share and NTFS permissions were correctly configured
  throughout. No permission changes were required - only a
  group membership addition. Ticket resolved.
──────────────────────────────────────────────────────────────────
```

---

## Troubleshooting Logic

When an access denied error appears on a network share, the diagnosis follows a fixed sequence. Working through each layer in order prevents incorrect changes and identifies the real cause quickly.

| Step | Question to ask | Tool to use | If yes | If no |
|---|---|---|---|---|
| 1 | Can the server be reached at all? | `ping [DC_IP]` | Move to step 2 | Fix network connectivity first |
| 2 | Is the share visible on the network? | `net view \\[DC_IP]` | Move to step 3 | Check Advanced Sharing is enabled |
| 3 | Is the user in the correct group? | ADUC → User → Member Of | Move to step 4 | Add user to group, log off and on |
| 4 | Does the group have share permissions? | Folder → Sharing → Advanced Sharing → Permissions | Move to step 5 | Add group with Change + Read |
| 5 | Does the group have NTFS permissions? | Folder → Properties → Security | Access should work | Add group with appropriate NTFS rights |

> In this lab the fault was at step 3. The share and NTFS permissions were correctly configured. Only the group membership was missing. This is the most common cause of access denied errors in Active Directory environments - the permissions are fine, but the user was never added to the group that holds them.

---

## Outcome and Validation

| Check | Result |
|---|---|
| DepartmentShare folder created on server | Pass |
| Advanced Sharing enabled with correct share name | Pass |
| Department_Sales granted Change and Read on share permissions | Pass |
| Department_Sales granted Read, Write, Read & Execute on NTFS | Pass |
| Department_Sales group created as Global Security in ADUC | Pass |
| Jayson Sule created and added to Department_Sales | Pass |
| Z: drive mapped successfully on client as \\\\[DC_IP]\\DepartmentShare | Pass |
| Jayson Sule can open share and create files | Pass |
| Otobong Mkpong receives access denied when not in group | Pass |
| Otobong Mkpong access restored after adding to Department_Sales | Pass |

---

## What I Learned

1. **Permissions are always two layers deep on a network share.** Share permissions control who can connect over the network. NTFS permissions control what they can do once inside. Both must allow access. Knowing this immediately halves the diagnostic search space when an access denied error appears.

2. **Permissions should never be assigned directly to individual users.** Granting access to a security group and controlling membership means the share configuration never needs to change as staff join, leave, or transfer teams. The correct fix for a missing access problem is a group membership change, not a permission change.

3. **A user must log off and back on after group membership changes.** Kerberos authentication tokens are issued at login and contain a snapshot of group memberships at that moment. Adding a user to a group mid-session does not take effect until they log out and back in. Knowing this prevents unnecessary repeat troubleshooting calls.

4. **The diagnostic sequence matters.** Checking group membership before touching share or NTFS permissions saved time and avoided incorrect changes. If the group membership had been correct, the next step would have been share permissions, then NTFS. Working in order means you find the real cause without creating new problems.

5. **Access denied does not mean permissions are wrong.** In this case the permissions were correctly configured throughout. The fault was entirely in group membership. Reacting to access denied by changing permissions without first checking group membership is one of the most common mistakes in permissions troubleshooting.

---

## Real World Relevance

Permission errors on shared drives are among the most frequently raised tickets in any IT support environment that uses file servers or network shares. The pattern is almost always the same: a user changes roles, transfers departments or is onboarded without full setup and the group membership step is missed or forgotten.

The correct response is never to assign permissions directly to the user account. Doing so creates a maintenance problem. When that user changes roles again, the individual permission entry stays behind. Over time, files and folders accumulate orphaned permission entries that are difficult to audit and impossible to manage at scale. Security group membership is the right mechanism because removing a user from a group immediately revokes all access that the group controls, across every resource it has been granted.

Understanding the relationship between share permissions and NTFS permissions is also a genuine differentiator in a support role. Many junior technicians know that permissions exist but do not understand the two-layer model. When they encounter access denied, they tend to escalate or guess. A technician who can walk the diagnostic sequence, identify which layer is blocking access, and make a targeted fix without touching anything else is one that managers trust to work independently. That is the practical value of this lab.

---

## Troubleshooting Reference

| Symptom | Likely Cause | Resolution |
|---|---|---|
| Access denied immediately on connecting to share | User not in the security group holding share permissions | Add user to the correct security group in ADUC; log off and on |
| Share visible but files cannot be written | NTFS permissions missing Write or Modify | Check Security tab on folder; add Write to group's NTFS entry |
| Share not visible on the network | Advanced Sharing not enabled or share name wrong | Re-check Advanced Sharing is ticked and share name matches UNC path |
| Drive maps but disconnects after reboot | Reconnect at sign-in not ticked during mapping | Re-map the drive with Reconnect at sign-in enabled |
| User added to group but still denied | Token not refreshed - user still logged in with old token | Ask user to log off and log back on to refresh Kerberos ticket |
| Everyone group in share permissions | Overly permissive default not cleaned up | Remove Everyone; add only the appropriate security group |
| NTFS permissions correct but still denied | Share permissions are more restrictive | Check share permissions - effective access is the most restrictive combination |
