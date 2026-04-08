# New User Onboarding and Offboarding Checklist

> **Author:** Nnamso Mkpong
>
> **Domain:** Windows Server - Active Directory, Device Management, Software Deployment, Access Control
>
> **Environment:** Windows Server 2022 (Domain Controller), Windows 11 Client VM, Active Directory Users and Computers, [domain] domain
>
> **Completed:** April 2026

---

## Objective

Run a complete end-to-end support workflow for a new starter and a departing employee. Provision a user account in Active Directory, create a department security group, join a device to the domain, install required software, configure email access, map a shared network drive, produce a Day One handover note, and then execute a full offboarding workflow covering account disablement, group removal, password reset, device collection, and compliance documentation.

---

## Business Scenario

**Onboarding:** HR has submitted a request for Maria Shima, a new Finance Department employee starting on 15 April 2026. She needs a domain account, department group membership, a laptop joined to the domain, Microsoft Office, Teams, and Google Chrome installed, and access to the Finance shared drive. The device must be ready before her first day.

**Offboarding:** Two weeks later, Maria Shima resigns. Her last day is 30 April 2026. Her manager Heidi Hogan submits an offboarding request on 29 April requiring account disablement, group membership removal, password reset, laptop and headset collection, and a data wipe before the device is redeployed.

Both workflows are documented with evidence at every step because in a regulated environment, incomplete onboarding creates access control gaps and incomplete offboarding creates security incidents.

---

## Environment and Tools Used

| Component | Detail |
|---|---|
| **Domain Controller OS** | Windows Server 2022 |
| **Domain Name** | [domain] |
| **Client Machine** | WIN11_CLIENT01 (Windows 11) |
| **Identity Tool** | Active Directory Users and Computers (ADUC) |
| **User Account** | maria.shima@[domain] |
| **Department Group** | Finance_Department (Global, Security) |
| **Software Installed** | Microsoft Teams, WPS Office, Google Chrome, Microsoft 365 |
| **Shared Drive** | \\[dc_ip]\Finance_Share — mapped as X: |
| **Email Workflow** | Microsoft 365 licence assignment (documented workflow) |

---

## Onboarding and Offboarding Process Map

> **Every step in both workflows maps to a security or access control requirement. Skipping any step either creates a gap in access provisioning or leaves a security exposure when a user departs.**

```
ONBOARDING WORKFLOW
  Step 1 — Receive and log onboarding ticket
       ↓
  Step 2 — Create AD user account (identity layer)
       ↓
  Step 3 — Create department group and assign membership (access layer)
       ↓
  Step 4 — Join device to domain (device layer)
       ↓
  Step 5 — Document email access workflow (communication layer)
       ↓
  Step 6 — Install required software (productivity layer)
       ↓
  Step 7 — Create shared folder and map network drive (resource access layer)
       ↓
  Step 8 — Produce Day One handover note (handover layer)
       ↓
  Onboarding complete — user ready for first day

OFFBOARDING WORKFLOW
  Step 1 — Receive and log offboarding ticket (with manager authorisation)
       ↓
  Step 2 — Disable AD account (blocks all authentication immediately)
       ↓
  Step 3 — Remove group memberships (removes resource access)
       ↓
  Step 4 — Reset password (prevents account reactivation by ex-user)
       ↓
  Step 5 — Collect device and complete hardware checklist (physical security)
       ↓
  Step 6 — Confirm data wipe required and schedule reimaging (data security)
       ↓
  Offboarding complete — access fully removed, device secured
```

---

## ONBOARDING WORKFLOW

---

### Phase 1 - Receive and Log the Onboarding Request

**Step 1.1 - Review the Onboarding Ticket**

The onboarding request arrives from HR with all required information. Before any provisioning begins, confirm the ticket contains a start date, department, manager name, required applications, and hardware requirements. A ticket missing any of these fields should be returned to HR for completion before work begins.

<img width="752" height="510" alt="01 Onboarinding ticket" src="https://github.com/user-attachments/assets/e6c33a77-5134-4ff3-8585-75ce29e4311d" />


> **Highlighted:** The Name, Start Date, and Required Apps fields are the three most operationally critical fields. Name determines the account naming convention. Start Date creates the provisioning deadline. Required Apps drives the software installation checklist. All three are confirmed present before any work begins.

---

### Phase 2 - Create the Active Directory User Account

**Step 2.1 - Create the User Account in ADUC**

Open Active Directory Users and Computers on the domain controller. Navigate to the appropriate Organisational Unit (in this lab, [domain]/Users). Right-click and select New > User. Complete all required fields following the organisation's naming convention.

<img width="433" height="375" alt="02 user creation" src="https://github.com/user-attachments/assets/4b060f40-2f65-443b-8f6b-5631ea662018" />



> **Highlighted:** The user logon name (maria.shima@m[domain]) is the UPN that will be used for all Microsoft 365 and domain authentication. Consistent naming conventions across all accounts make administration, auditing, and password reset requests significantly easier to manage at scale.

---

**Step 2.2 - Confirm the Account Appears in ADUC**

After saving the account, refresh the Users container and confirm Maria Shima appears in the list as a User object.

<img width="655" height="520" alt="03 user created in aduc" src="https://github.com/user-attachments/assets/d203d7ba-34c1-42c6-8723-7419e2ba6ed5" />


> **Highlighted:** The Maria Shima User object is visible in ADUC alongside other accounts. 

---

### Phase 3 - Create the Department Security Group and Assign Membership

**Step 3.1 - Create the Finance_Department Security Group**

Navigate to the Users container in ADUC. Right-click and select New > Group. Create a Global Security group named Finance_Department.

<img width="435" height="377" alt="04 finance_dept group created" src="https://github.com/user-attachments/assets/714464d9-3974-4f42-9f23-304c8745c1b6" />


> A Global Security group is the correct choice for department resource access control. It can be assigned to shared folders, printers, and other resources across the domain. Using individual user accounts for resource permissions instead of groups creates an unmanageable permission model as the organisation grows.

---

**Step 3.2 - Add Maria Shima to the Finance_Department Group**

Open Maria Shima's account properties and navigate to the Member Of tab. Click Add and search for Finance_Department.

<img width="456" height="249" alt="06 adding user to group" src="https://github.com/user-attachments/assets/10fabda0-6d5e-47a1-b6db-07fc38b759d1" />


> **Highlighted:** The Finance_Department group is entered in the object name field and confirmed via Check Names. 
---

**Step 3.3 - Confirm Group Membership**

Return to the Member Of tab on Maria Shima's account properties and confirm both group memberships are present: Domain Users (default) and Finance_Department (newly assigned).

<img width="410" height="536" alt="07 user group membership confirmed" src="https://github.com/user-attachments/assets/8b88a3d0-ccc9-4cb9-a02a-8de01e581147" />


> **Highlighted:** Two group memberships are confirmed. Domain Users is the default membership for all domain accounts. Finance_Department is the department-specific group that will control access to the Finance shared drive and any other Finance-scoped resources. 
---

### Phase 4 - Join the Device to the Domain

**Step 4.1 - Configure Domain Join on WIN11_CLIENT01**

On the Windows 11 client machine, open System Properties (sysdm.cpl) and click Change. Select Domain and enter [domain]. Provide domain administrator credentials when prompted.

<img width="319" height="385" alt="08 domain name join dialog" src="https://github.com/user-attachments/assets/108e0f29-5035-4ef1-87ff-db1231ba9234" />


> **Highlighted:** The computer name WIN11_CLIENT01 follows the organisation's device naming convention. The domain is set to [domain]. The DNS server on this client must point to the domain controller IP address for the domain join to resolve correctly.

---

**Step 4.2 - Confirm Domain Join in System Properties**

After the restart, open System Properties and confirm the machine shows the full computer name and domain.

<img width="411" height="465" alt="09 device joined sucessfully " src="https://github.com/user-attachments/assets/ba03df66-d8cb-4ba9-a9aa-4b69046f6388" />


> **Highlighted:** The full computer name reads WIN11_CLIENT01.[domain] and the Domain field shows [domain]. The device is now a domain member. Domain users can log in to this machine and Group Policy will apply on the next boot.

---

**Step 4.3 - Verify Device Appears in ADUC Computers Container**

Return to Active Directory Users and Computers on the domain controller. Navigate to the Computers container and confirm WIN11_CLIENT01 appears as a Computer object.

<img width="856" height="602" alt="10 cdevice visible in ADCU" src="https://github.com/user-attachments/assets/f1cd967a-e6fa-4203-b9a6-8545c0cde56d" />


> **Highlighted:** WIN11_CLIENT01 is registered in the Computers OU under [domain]. This confirms the domain join completed successfully and the machine is known to Active Directory. Group Policy Objects assigned to the Computers container will apply to this device.

---

### Phase 5 - Configure Email Access

**Step 5.1 - Document the Microsoft 365 Email Provisioning Workflow**

Email provisioning in Microsoft 365 requires actions in the M365 admin centre rather than in Active Directory. The workflow was documented rather than executed in this lab VM because a live M365 tenant was not available in the test environment.

<img width="477" height="401" alt="11 email access workflow" src="https://github.com/user-attachments/assets/887b688f-6277-454f-9948-6c724c5695ce" />


Email provisioning workflow for Maria Shima:

| Step | Action | Performed by |
|---|---|---|
| 1 | IT submits licence request to M365 admin | IT Support |
| 2 | Admin assigns Microsoft 365 licence to maria.shima@[domain] | M365 Admin |
| 3 | Exchange Online mailbox created automatically | Automatic |
| 4 | IT verifies access via Outlook or OWA | IT Support |
| 5 | Confirmation sent to manager Heidi Hogan | IT Support |

> Documenting the workflow when a live system is not available is the correct approach for this lab. It demonstrates understanding of the process without misrepresenting what was executed in the lab environment.

---

### Phase 6 - Install Required Software

**Step 6.1 - Install Microsoft Teams, WPS Office, and Supporting Applications**

Install all applications listed on the onboarding ticket. Verify each installation in Settings > Apps > Installed Apps.

<img width="1035" height="671" alt="12teams and wps office screenshot" src="https://github.com/user-attachments/assets/7aed9977-8792-4cfe-9b4e-670ee79755c3" />


> **Highlighted:** Microsoft Teams is confirmed installed at version 1.25.28902 (4/7/2026) and WPS Office at version 12.2.0.23196. Both are visible in the Installed Apps list-

---

**Step 6.2 - Confirm Google Chrome and Microsoft 365 Installation**

<img width="1042" height="420" alt="13 google screenshot" src="https://github.com/user-attachments/assets/2789c756-0e6d-4d90-bd6a-64b75df6011a" />


> **Highlighted:** Google Chrome version 146.0.7680.178 is confirmed installed (4/7/2026). Microsoft 365 version 16.0.19822.20142 is confirmed installed (4/7/2026). All three applications from the onboarding ticket are now installed and verified.

Software installation summary:

| Application | Version | Status |
|---|---|---|
| Microsoft Teams | 1.25.28902 | Installed |
| WPS Office | 12.2.0.23196 | Installed |
| Google Chrome | 146.0.7680.178 | Installed |
| Microsoft 365 | 16.0.19822.20142 | Installed |

---

### Phase 7 - Create Shared Folder and Map Network Drive

**Step 7.1 - Confirm Finance_Share Folder is Shared on the File Server**

On the file server, navigate to the Finance_Share folder properties and confirm it is shared with the correct network path.

<img width="358" height="480" alt="14 finance share folder properties" src="https://github.com/user-attachments/assets/7527a9ff-e2da-4d58-ae71-c5a95ef66522" />


> **Highlighted:** The network path \\[dc_ip]\Finance_Share confirms the folder is shared on the file server. NTFS permissions should be configured to allow the Finance_Department security group Read and Write access, and deny access to all other groups. The Finance_Department group assigned in Step 3 controls who can access this share.

---

**Step 7.2 - Map the Network Drive on the Client Machine**

On WIN11_CLIENT01, map the Finance_Share as a persistent network drive. The drive is mapped to X: from \\[dc_ip]\Finance_Share.

<img width="783" height="546" alt="15 NETWORK MAPPING" src="https://github.com/user-attachments/assets/ca2905f1-87ac-4da5-9f8f-0ac024f5761c" />


> **Highlighted:** Finance_Share (\\[dc_ip]) is visible in File Explorer under Network Locations, mapped as drive X: with 42.6 GB free of 59.3 GB. The drive is accessible from the client machine. Maria Shima's Finance_Department group membership grants her access to this share automatically.

---

### Phase 8 - Produce the Day One Handover Note

**Step 8.1- Create the Handover Document for the New User**

Before the user's first day, produce a handover note summarising everything that has been provisioned. This document is given to the user and a copy is attached to the ticket.

<img width="588" height="598" alt="16 day one hand over note" src="https://github.com/user-attachments/assets/aa3fa475-01d1-4b19-9ace-74f114a3ee17" />


> **Highlighted:** All three sections confirm the provisioning checklist is complete. Login credentials are documented for handover. Software installed matches the onboarding ticket requirements exactly. Access granted includes domain membership, department group, and the shared drive path.

---

## OFFBOARDING WORKFLOW

---

### Phase 9 - Receive and Log the Offboarding Request

**Step 9.1 - Review the Offboarding Ticket**

The offboarding request is submitted by Heidi Hogan (Maria Shima's manager) on 29 April 2026. Last day of employment is 30 April 2026. All actions must be completed by end of business on the last day.

<img width="581" height="600" alt="17 Offboarding ticket" src="https://github.com/user-attachments/assets/ad97b2df-c768-4c3a-937c-3d000ac99ae3" />

> **Highlighted:** The action items are listed in the correct order of priority. The account must be disabled first  before any notification is sent to the user  to prevent the departing employee from taking further action in company systems. Group removal and password reset follow to harden the disabled account.

---

### Phase 10 - Disable the Active Directory Account

**Step 10.1 - Disable Maria Shima's Account in ADUC**

Right-click on Maria Shima's account in ADUC and select Disable Account. Confirm the action when prompted.

<img width="290" height="152" alt="18 disable maria account" src="https://github.com/user-attachments/assets/b3449bde-01e3-44fc-be4d-686959d22fdc" />


> **Highlighted:** The confirmation dialog reads Object Maria Shima has been disabled. The account is now unable to authenticate to any domain resource. The user cannot log in to Windows, cannot access email , and cannot connect to the VPN. This is the most critical single action in the offboarding workflow.

---

### Phase 11 - Remove Group Memberships

**Step 11.1 - Remove Maria Shima from Finance_Department Group**

Open the Finance_Department group properties. Navigate to the Members tab. Select Maria Shima and click Remove. Confirm when prompted.

<img width="401" height="452" alt="19 remove maria from group" src="https://github.com/user-attachments/assets/f61cd322-cb7f-4b80-95dc-599710edd1f4" />


> **Highlighted:** The confirmation dialog asks whether to remove the selected member from the group. Maria Shima is listed as the member being removed from Finance_Department ([domain]/Users). After clicking Yes, she will no longer have access to any resource controlled by the Finance_Department group, including the Finance_Share network drive.

---

### Phase 12 - Reset the Password

**Step 12.1 - Reset Password and Configure Account Options**

Open Maria Shima's account properties in ADUC and navigate to the Account tab. Set a strong temporary password and ensure the User must change password at next logon checkbox is ticked. This prevents the account from being reactivated with the original credentials if it is accidentally re-enabled in the future.

<img width="413" height="540" alt="20 user must change password" src="https://github.com/user-attachments/assets/341c392b-9ae7-4f04-a75b-19903d0cd98b" />


> **Highlighted:** User must change password at next logon is ticked. This means the password is now set to an IT-controlled value that the departing user does not know. Even if the account were accidentally re-enabled, the user could not log in without contacting IT.

---

### Phase 13 - Collect the Device and Complete the Hardware Checklist

**Step 13.1 - Complete the Device Collection Checklist**

Before the device is wiped and redeployed, complete a physical hardware checklist confirming what was returned, the condition of each item, and who collected it.

<img width="586" height="600" alt="21 Device collection checlist" src="https://github.com/user-attachments/assets/b4f6afd9-a295-47d0-9276-152db5c6a38b" />



> **Highlighted:** Hardware returned, device condition, and data wipe requirement are all recorded. The collection was performed by Nnamso (technician name recorded). The device is noted as ready for reimaging and redeployment. This checklist forms part of the audit trail that confirms no company assets were retained by the departing employee.

---

## Business Impact Note

> **Onboarding and offboarding are the two workflows where IT support has the highest direct impact on business security and operational continuity — and where mistakes are the most costly.**

An incomplete onboarding means a new employee cannot start work on their first day. They cannot log in, access their email, find their team's shared files, or use the tools they need. The cost is not just the new employee's lost productivity - it is the management time spent chasing IT, the poor first impression of the organisation, and the risk of the user finding workarounds that bypass security controls.

An incomplete offboarding is a security incident waiting to happen. A departing employee whose account is still active, group memberships are still in place, and password is unchanged retains full access to company data until someone notices. In a regulated environment, this is not just a policy violation - it is a reportable compliance event. A clear offboarding checklist completed on the last day of employment with evidence at every step is the minimum standard for any managed IT environment.

The combination of both workflows in a single lab demonstrates that the technician understands the full user lifecycle — not just the technical steps, but the security reasoning behind the order of those steps and the documentation required to prove they were completed.

---

## Help Desk Ticket Notes

See `TICKET-0094-onboarding-maria-shima.md` and `TICKET-0099-offboarding-maria-shima.md` in this folder.

---

## Outcome and Validation

| Check | Result |
|---|---|
| Onboarding ticket received and all fields confirmed | Pass |
| AD user account created: maria.shima@mylab.local | Pass |
| Finance_Department security group created (Global, Security) | Pass |
| Maria Shima added to Finance_Department and Domain Users | Pass |
| WIN11_CLIENT01 joined to mylab.local domain | Pass |
| Device visible in ADUC Computers container | Pass |
| Email provisioning workflow documented | Pass |
| Microsoft Teams, WPS Office, Google Chrome, Microsoft 365 installed | Pass |
| Finance_Share confirmed shared on file server | Pass |
| Finance_Share mapped as X: on WIN11_CLIENT01 | Pass |
| Day One handover note produced and confirmed complete | Pass |
| Offboarding ticket received with manager authorisation | Pass |
| Maria Shima AD account disabled — confirmation dialog confirmed | Pass |
| Maria Shima removed from Finance_Department group | Pass |
| Password reset with must change at next logon enabled | Pass |
| Device collection checklist completed — all items accounted for | Pass |
| Data wipe scheduled — device ready for reimaging | Pass |

---

## What I Learned

1. **Onboarding is a checklist discipline, not just a technical task.** Every step in the provisioning workflow is a dependency for the next one. Creating the account before the group means the group assignment can happen in the same ADUC session. Joining the device before software installation means domain group policies are applied before the apps are configured. The order matters, and a checklist makes the order repeatable for every new starter regardless of which technician handles the request.

2. **Group-based access control is always preferable to individual user permissions.** Assigning Maria Shima to Finance_Department and then giving Finance_Department access to the Finance_Share means that when Maria leaves, removing her from the group removes all her resource access in one action. If permissions had been assigned directly to her account on 12 different resources, offboarding would require finding and removing 12 separate permission entries — and missing one is easy.

3. **The offboarding sequence has a security-critical order.** Disabling the account must happen first, before any communication is sent to the user and before any other step is performed. Removing groups before disabling the account is a missed window during which the user still has full access. Resetting the password before disabling is pointless because the account is still active. The sequence is: disable first, then clean up.

4. **Documentation at every step protects the technician as much as the organisation.** A ticket that records exactly when the account was disabled, who approved the offboarding, and that all hardware was returned creates an audit trail that protects IT if a security incident is later traced back to a former employee. Without this documentation, IT cannot prove when access was removed or who authorised the action.

5. **Day One readiness is a measurable outcome, not a feeling.** The handover note in this lab lists exactly what the user will have when they sit down on their first morning: the laptop name, the login credentials, the email address, the software installed, and the network drive path. If any item on that list is missing, the user notices immediately. If every item is confirmed, the first day starts without a support call.

---

## Real World Relevance

Onboarding and offboarding together represent the full user lifecycle and are the single most complete demonstration of IT support competency in any portfolio. They require Active Directory administration, device provisioning, software deployment, network resource management, security-aware process execution, and professional documentation — all in one workflow. No other lab category requires all of these skills simultaneously.

In interview scenarios for help desk, desktop support, and IT analyst roles, onboarding and offboarding questions are among the most commonly used assessment questions because they reveal whether the candidate thinks systematically or reactively. A candidate who can describe the order of offboarding actions and explain why the account must be disabled before the password is reset is demonstrating security awareness. A candidate who produced and published a complete end-to-end lab with annotated screenshots and a full ticket note is demonstrating portfolio discipline.

In managed service environments, this type of structured onboarding and offboarding workflow is executed dozens of times per month. Technicians who can complete it without missing steps, document it without prompting, and communicate the result to the manager and the user professionally are the ones who progress from first-line to second-line and into team lead roles.

---

## Troubleshooting Reference

| Situation | Correct Action | Common Mistake |
|---|---|---|
| New user cannot log in on first day | Confirm account is enabled, password not expired, and device is domain joined | Checking the laptop before checking the AD account |
| Domain join fails during device setup | Confirm DNS on the client points to the domain controller, not a public DNS server | Attempting to join domain with public DNS (8.8.8.8) configured |
| User cannot access Finance_Share | Confirm Finance_Department group membership and NTFS permissions on the share | Checking only the share permissions, ignoring NTFS permissions |
| Offboarding request missing manager authorisation | Return the ticket to the requestor — do not disable the account without confirmed authorisation | Disabling account based on verbal instruction without written authorisation |
| Account disabled but user still has active sessions | Force sign-out via Active Directory or M365 admin centre — disabling AD blocks new logins but does not terminate active sessions | Assuming account disable ends all active sessions immediately |
| Device not visible in ADUC after domain join | Confirm the domain join completed and a restart occurred — computers appear in ADUC after first reboot | Looking in ADUC before the client machine has rebooted |
| Shared drive not accessible after mapping | Confirm group membership is active and NTFS permissions include the security group — mapping a drive without correct permissions shows the drive but denies access | Mapping the drive letter correctly but assigning permissions to the individual user instead of the group |
