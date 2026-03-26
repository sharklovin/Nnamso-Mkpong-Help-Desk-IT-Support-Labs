# Lab 02: User Account Creation and Management

> **Author:** Nnamso Mkpong
> 
> **Domain:** Active Directory Administration
> 
> **Environment:** Windows Server 2022 + Windows 11 Client (Virtualised)  
> **Completed:** March 2026

---

## Objective

Create a new domain user account inside a dedicated Organisational Unit, populate all relevant profile attributes, assign the account to an appropriate security group, disable the account in response to an HR suspension request and then re-enable it when access is reinstated.

---

## Business Scenario

> **Ticket #0051 | New Starter Provisioning + Temporary Suspension**
>
> HR has submitted a new starter request for **Hogan Hogan**, joining the Sales department as a Sales Assistant. The account needs to be created, fully populated with contact and organisational details and added to the Sales security group before their start date.
>
> A follow-up request arrives shortly after: HR asks for access to be suspended temporarily while an onboarding matter is resolved. Once cleared, they request the account be reinstated.

This scenario covers two of the most routine tasks in any IT support role which is provisioning a new account and managing the user lifecycle through disable and re-enable actions.

---

## Environment and Tools Used

| Component | Detail |
|---|---|
| **Server OS** | Windows Server 2022 Evaluation |
| **Domain** | mylab.local |
| **Tool** | Active Directory Users and Computers (ADUC) |
| **Access path** | Server Manager → Tools → ADUC |
| **User created** | Hogan Hogan (Hcode@mylab.local) |
| **OU created** | Office Users |
| **Group created** | Sales (Global Security) |

---

## Lab Architecture

```
mylab.local
│
├── Builtin
├── Computers
├── Domain Controllers
├── Users
│     └── (default domain users)
│
└── Office Users          ← OU created in this lab
      ├── Hogan Hogan     ← User created in this lab
      └── Sales           ← Security group created in this lab
```

---

## Steps Performed

---

### Phase 1 - Access Active Directory Users and Computers

**Step 1.1 - Open Server Manager**

Press the `Windows` key, type **Server Manager**, and open it. This is the central administration console on Windows Server from which all AD tools are accessed.

<img width="758" height="674" alt="Open server maanger" src="https://github.com/user-attachments/assets/111c3346-b642-4e5b-b8e4-ebef56020186" />


---

**Step 1.2 - Navigate to ADUC via Tools Menu**

Inside Server Manager, click **Tools** in the top-right menu bar and select **Active Directory Users and Computers** from the dropdown list.

<img width="1009" height="724" alt="click tools" src="https://github.com/user-attachments/assets/76d97b64-1878-4388-b622-ce407d93745a" />


> ADUC is the primary interface for all user, group and OU management tasks. Every action in this lab is performed from here.

---

### Phase 2 - Create the Organizational Unit

**Step 2.1 - Right-click the Domain to Create a New OU**

In ADUC, right-click the domain root **mylab.local** in the left panel, hover over **New**, and select **Organizational Unit** from the submenu.

<img width="1012" height="725" alt="ON domain right click to Organization Unit" src="https://github.com/user-attachments/assets/1f9bfa22-4fd8-437d-b9d9-9e916ebac84f" />


> **You may ask why create an OU?** Organisational Units allow administrators to logically group user accounts by department, location or function. This makes it easier to apply Group Policies selectively, delegate control to managers and keep the directory clean and well-structured. Dumping every user into the default Users container is considered poor practice in a real environment.

---

**Step 2.2 - Name the Organisational Unit**

In the New Object – Organizational Unit dialog, enter the name **Office Users**. Leave the **Protect container from accidental deletion** checkbox ticked. Click **OK**.

<img width="438" height="373" alt="ORG UNIT" src="https://github.com/user-attachments/assets/248b605f-3a31-4a89-81e5-aa633197353a" />


> The accidental deletion protection checkbox prevents the OU from being removed with a simple right-click delete. In a production environment this is always left enabled to avoid catastrophic mistakes.

The **Office Users** OU now appears in the left panel under mylab.local.

---

### Phase 3 - Create the New User Account

**Step 3.1 - Right-click Office Users → New → User**

Right-click the newly created **Office Users** OU in the left panel, hover over **New** and select **User**.

<img width="854" height="640" alt="SELECT TO CREATE OFFICE USER" src="https://github.com/user-attachments/assets/75150d66-c9ca-4f54-aaa9-d32ddbc98572" />


> Creating the user directly inside the Office Users Office Users means they are organised from the moment of creation. No need to move them later.

---

**Step 3.2 - Fill in the User Details**

Complete the New Object - User wizard with the following details:

| Field | Value |
|---|---|
| First name | Hogan |
| Last name | Hogan |
| Full name | Hogan Hogan |
| User logon name | Hcode |
| Domain | @mylab.local |

Click **Next**.

<img width="438" height="387" alt="CREATE NEW OFFICE USER" src="https://github.com/user-attachments/assets/42658f98-031c-4351-8657-ca10666d25a8" />


---

**Step 3.3 - Set the Initial Password**

Enter and confirm an initial password. Password options are left at their defaults for now. These can be adjusted later depending on the organisation's onboarding policy.

Click **Next**.

<img width="437" height="377" alt="CREATING PASSWORD" src="https://github.com/user-attachments/assets/5ad0e555-d81c-416f-837b-6971a509c9d3" />


---

**Step 3.4 - Confirm and Finish**

Review the summary. The object to be created is confirmed as:

- Full name: Hogan Hogan
- User logon name: Hcode@mylab.local

Click **Finish**.

<img width="437" height="380" alt="CLICK FINISH" src="https://github.com/user-attachments/assets/e83a3e59-2590-44d6-baf0-53cdd4b0f7c3" />



<img width="847" height="627" alt="hogan appears" src="https://github.com/user-attachments/assets/ee34ae51-1b26-4e34-84ed-8437c251d724" />

The user **Hogan Hogan** now appears in the Office Users OU.

---

### Phase 4 - Populate User Attributes

With the account created, the next step is to populate the profile attributes. In a real environment these details come from the HR new starter form and are essential for directory accuracy, helpdesk lookup and access management.

**Step 4.1 - Open User Properties**

Right-click **Hogan Hogan** in the Office Users OU and select **Properties**.

<img width="847" height="627" alt="ADD PROPERTIES" src="https://github.com/user-attachments/assets/954adb6e-19d3-4038-9991-9e7dcc2e23d2" />


---

**Step 4.2 - Populate the General Tab**

On the **General** tab, fill in the following:

| Field | Value |
|---|---|
| Office | Mylab |
| Telephone number | +2349062234511 |
| Email | Nnamsotechie@gmail.com |

Click **Apply**.

<img width="408" height="555" alt="FILL IN GENERAL TAB" src="https://github.com/user-attachments/assets/b8ccf786-c7d3-4a8b-b3e8-3115c3726502" />


---

**Step 4.3 - Populate the Organization Tab**

Navigate to the **Organization** tab and fill in:

| Field | Value |
|---|---|
| Job Title | Sales Assistant |
| Department | Sales |
| Company | My LAB |

Click **Apply**.

<img width="411" height="554" alt="FILL IN ORGANIZATION TAB" src="https://github.com/user-attachments/assets/6d56b097-9762-4ac1-938c-1b3989195ce8" />


> The Organization tab is critical for directory search. Accurate data here reduces manual corrections later.

---

**Step 4.4 - Populate the Telephones Tab**

Navigate to the **Telephones** tab and enter the home number:

| Field | Value |
|---|---|
| Home | 0806 111 1234 |

Click **Apply**, then **OK** to close Properties.

<img width="408" height="546" alt="FILL IN TELEPHONE " src="https://github.com/user-attachments/assets/d8a93e4a-632b-40a6-addc-57fc687b3176" />


---

### Phase 5 - Create the Sales Group and Assign Membership

**Step 5.1 - Create the Sales Security Group**

Before assigning the user to a group, the Sales group needs to exist. Right-click the **Office Users** OU, hover over **New** and select **Group**.

<img width="849" height="631" alt="cREATING NEW GROUP" src="https://github.com/user-attachments/assets/bdb0a732-0555-4927-828e-dc8379c1781f" />

> If you already have a custom group like Sales, use that. For this Lab Environment there is no Sales Group exist so the best thing to do is to create a new group first.


---

**Step 5.2 - Configure the Group**

In the New Object - Group dialog, enter the following:

| Field | Value |
|---|---|
| Group name | Sales |
| Group scope | Global |
| Group type | Security |

Click **OK**.

<img width="436" height="381" alt="TYPING GROUP" src="https://github.com/user-attachments/assets/590cd132-730e-4f96-b583-d0890564875d" />


> **You may ask why Global Security?** A Global Security group is the standard choice for grouping users within a single domain who need the same access permissions. 

---

**Step 5.3 - Open User Properties to Access Member Of Tab**

Right-click **Hogan Hogan** and select **Properties** to navigate to the group membership settings.

<img width="852" height="629" alt="LOOKING FOR MEMBER OF" src="https://github.com/user-attachments/assets/3da10e8d-8ea2-404d-ade9-d5a2093d4e15" />


---

**Step 5.4 - Navigate to Member Of and Click Add**

Go to the **Member Of** tab. The user currently only belongs to the default **Domain Users** group. Click **Add** to assign an additional group.

<img width="405" height="551" alt="CLICK ON ADD" src="https://github.com/user-attachments/assets/0ba56f85-e269-441a-b42a-8e41d46dbc55" />


---

**Step 5.5 - Search for and Add the Sales Group**

In the Select Groups dialog, type **Sales** in the object names field and click **Check Names** to resolve and validate the group name against the directory.

<img width="456" height="253" alt="ADD SALES AND CLICK CHECK NAMES" src="https://github.com/user-attachments/assets/aa3d87d9-5ac9-4bcd-8852-fe086a3539e0" />

Click **OK** to confirm.

---

**Step 5.6 - Confirm Group Membership**

Back on the Member Of tab, **Sales** now appears alongside **Domain Users**, with the path confirmed as `mylab.local/Office Users`.

<img width="411" height="543" alt="MEMBER OF TAB AND SEE SALES" src="https://github.com/user-attachments/assets/71193992-6de8-49d6-8b88-c4a5d87e6b10" />



Click **OK** to save and close.

---

### Phase 6 - Disable the Account

HR has submitted a follow-up request to suspend access temporarily while an onboarding matter is being resolved.

**Step 6.1 - Right-click the Account and Select Disable Account**

Right-click **Hogan Hogan** in the Office Users OU and select **Disable Account** from the context menu.

<img width="850" height="629" alt="DISABLE ACCOUNT HOGAN" src="https://github.com/user-attachments/assets/474f4b06-f3a0-49c9-81b5-abb58baaf5ef" />


> Disabling is always preferred over deleting when the suspension is temporary. A disabled account retains all group memberships, attributes and profile data. 

---

**Step 6.2 - Confirm the Disable Action**

Active Directory Domain Services displays a confirmation:

> *"Object Hogan Hogan has been disabled."*

The user object in ADUC shows a downward arrow icon on the account, visually indicating the disabled state.

<img width="649" height="542" alt="ACCOUNT DISABLE" src="https://github.com/user-attachments/assets/fb171d9a-4d49-4e16-87c0-7417436e4c2d" />


---

### Phase 7 - Re-enable the Account

HR has confirmed the onboarding matter is resolved and requests that access be reinstated.

**Step 7.1 - Right-click the Account and Select Enable Account**

Right-click **Hogan Hogan** and select **Enable Account**.

<img width="848" height="622" alt="ENABLE ACCOUNT" src="https://github.com/user-attachments/assets/11c840ab-618a-4e07-9eb3-7bdb0a6d038a" />



Notice the context menu now shows **Enable Account** instead of Disable Account, confirming the account was in a disabled state.

---

**Step 7.2 - Confirm the Re-enable Action**

Active Directory Domain Services confirms:

> *"Object Hogan Hogan has been enabled."*

The downward arrow icon disappears from the user object and the account returns to its normal active state with all attributes and group memberships fully intact.

<img width="848" height="627" alt="ACCOUNT REENABLED" src="https://github.com/user-attachments/assets/b1583321-b942-4eb4-b5cd-bafdc682aab5" />


---

## Help Desk Ticket Note

```
──────────────────────────────────────────────────────────────────
TICKET #0051 | NEW STARTER PROVISIONING + TEMPORARY SUSPENSION
Technician: Nnamso Mkpong | Date: March 2026 | Priority: STANDARD
──────────────────────────────────────────────────────────────────

ISSUE REPORTED:
  HR submitted a new starter request for Hogan Hogan, joining the
  Sales department as a Sales Assistant effective immediately.
  A follow-up request to temporarily suspend access was received
  shortly after provisioning.

ACTIONS TAKEN - PROVISIONING:
  1. Opened ADUC via Server Manager → Tools.
  2. Created Organisational Unit: Office Users under mylab.local.
  3. Created new user: Hogan Hogan (Hcode@mylab.local) inside
     the Office Users OU.
  4. Populated Organization tab: title (Sales Assistant),
     department (Sales), company (My LAB).
  5. Populated Telephones tab: home number added.
  6. Created Security Group: Sales (Global Security) in Office Users OU.
  7. Assigned user to Sales group via Properties → Member Of → Add.
  8. Confirmed group membership: Domain Users and Sales.

ACTIONS TAKEN - SUSPENSION:
  9. Received HR request to suspend access.
  10. Right-clicked Hogan Hogan → Disable Account.
  11. Confirmed AD notification: Object Hogan Hogan has been disabled.
  12. Verified downward arrow icon on account object in ADUC.

ACTIONS TAKEN — REINSTATEMENT:
  13. Received HR clearance to restore access.
  14. Right-clicked Hogan Hogan → Enable Account.
  15. Confirmed AD notification: Object Hogan Hogan has been enabled.
  16. Verified account icon returned to normal active state.

OUTCOME:
  User account created, attributed and assigned to Sales group.
  Account disabled and re-enabled without data loss. All group
  memberships and attributes preserved throughout. Ticket resolved.
──────────────────────────────────────────────────────────────────
```

---

## Group Membership Justification

| Group | Scope | Type | Reason Assigned |
|---|---|---|---|
| Domain Users | Global | Security | Default group - all domain accounts are members automatically |
| Sales | Global | Security | User's department is Sales. This group controls access to Sales shared folders, printers and departmental resources. Assigning at account creation ensures correct access from day one. |

> In a larger environment you might also add the user to groups such as VPN Users, Microsoft 365 Licensed Users, or departmental distribution lists. The principle is the same: assign only what the role requires. This is the foundation of least-privilege access management.

---

## Outcome and Validation

| Check | Result |
|---|---|
| Office Users OU created in mylab.local | Pass |
| User Hogan Hogan created inside Office Users OU | Pass |
| All profile attributes populated across General, Organization, and Telephones tabs | Pass |
| Sales security group created with Global scope and Security type | Pass |
| User assigned to Sales group and confirmed in Member Of tab | Pass |
| Account disabled and AD confirmation received with icon updated | Pass |
| Account re-enabled and AD confirmation received with icon restored | Pass |
| All attributes and group memberships intact after disable and re-enable cycle | Pass |

---

## What I Learned

1. **OUs are the foundation of good AD structure.** Creating a dedicated OU before adding users means accounts are organised from the start. It also makes applying Group Policy and delegating control far more manageable as the environment grows.

2. **User attributes go beyond just a name and password.** Populating the General, Organization and Telephones tabs makes the directory genuinely useful — for helpdesk lookup, for Microsoft 365 integration, and for access management decisions tied to department or role.

3. **Disable, never delete, for temporary suspensions.** Disabling preserves the account SID, group memberships, and all attributes. Deleting is permanent. If the same name comes back as a new hire, they get a new SID and lose all previous access configurations.

4. **Group membership should reflect role, not just convenience.** Assigning a user to the Sales group because they are in Sales is the right reason. The group should map to a real access need. This thinking is the first step toward practising least-privilege access control.

5. **The user lifecycle in AD is an ongoing process.** Provisioning, updating attributes, assigning groups, disabling and re-enabling are all part of managing accounts over time. Real support work involves all of these, often within the same ticket.

---

## Real World Relevance

User provisioning and account lifecycle management are among the most consistent daily responsibilities in any IT support role that operates within an Active Directory environment.

Every new starter generates at minimum one request: create the account, set it up correctly, assign it to the right groups, and make sure it is ready before they arrive. When that process is done well, the user logs in on day one with access to everything they need and nothing they do not. When it is done poorly you get missing attributes, wrong group, account sitting in the default Users container and other problems follow. The helpdesk receives follow-up tickets. Shared folder access fails. Reports pulled from the directory show incomplete data.

The disable workflow matters equally. When someone takes extended leave, changes roles, or needs access suspended while HR or management handles a matter, the ability to quickly and cleanly disable an account without deleting it is a key control. It is also an audit requirement in most organisations. Auditors expect departed or suspended employees to have accounts disabled promptly and want to see that action is logged, reversible and documented.

This lab covers the full provisioning and lifecycle cycle in one workflow. That is exactly why it is one of the most practically valuable skills to demonstrate for anyone pursuing a help desk, desktop support or junior systems administration position.

---

## Troubleshooting Reference

| Symptom | Likely Cause | Resolution |
|---|---|---|
| New OU not visible after creation | ADUC view needs refreshing | Press F5 or use View → Refresh in ADUC |
| User not appearing in Office Users OU | Created in the wrong container | Check other OUs and the default Users container; move if needed |
| Check Names fails when adding a group | Group name misspelled or group does not exist | Create the group first, then retry the membership assignment |
| Disable Account option is greyed out | Account may already be disabled | Check the icon — if it shows a down arrow it is already disabled |
| Attributes not saving after clicking Apply | Permissions issue or replication lag | Confirm admin rights; wait briefly and check again |
| Account not re-enabling as expected | Possible AD replication issue on multi-DC environment | Confirm you are connected to the correct DC; try from the PDC emulator |
