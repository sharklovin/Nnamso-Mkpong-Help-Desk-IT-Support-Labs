# Help Desk Ticket — #0051

**Category:** User Account Management - New Starter Provisioning and Temporary Suspension
**Priority:** Standard
**Status:** Resolved

---

## Ticket Details

| Field | Value |
|---|---|
| Ticket ID | 0051 |
| Date Opened | March 2026 |
| Date Resolved | March 2026 |
| Assigned To | Nnamso Mkpong — IT Support |
| Affected User | Hogan Hogan |
| Logon Name | Hcode@mylab.local |
| Department | Sales |
| Job Title | Sales Assistant |

---

## Request 1 - New Starter Provisioning

HR submitted a standard new starter form for Hogan Hogan. The account was required to be created in Active Directory with full profile attributes and assigned to the Sales security group prior to the user's start date.

**Actions taken:**

1. Opened Active Directory Users and Computers via Server Manager Tools
2. Created Organisational Unit: Office Users under mylab.local
3. Created user account: Hogan Hogan with logon name Hcode@mylab.local inside the Office Users OU
4. Set initial password meeting domain complexity requirements
5. Populated General tab with office location, phone number, and email address
6. Populated Organization tab with job title, department, and company name
7. Populated Telephones tab with home contact number
8. Created Sales security group (Global scope, Security type) in Office Users OU
9. Assigned user to Sales group via Properties — Member Of — Add
10. Confirmed group membership: Domain Users and Sales both showing in Member Of tab

**Outcome:** Account created and fully provisioned. User ready to log in on start date.

---

## Request 2 - Temporary Account Suspension

HR submitted a follow-up request asking for the account to be suspended temporarily while an onboarding administrative matter was being resolved.

**Actions taken:**

1. Located Hogan Hogan in ADUC under Office Users OU
2. Right-clicked account and selected Disable Account
3. Received AD confirmation: Object Hogan Hogan has been disabled
4. Verified downward arrow icon appeared on the account object confirming disabled state

**Outcome:** Account suspended. User cannot log in. All attributes and group memberships preserved.

---

## Request 3 - Account Reinstatement

HR confirmed the matter was resolved and requested the account be reactivated.

**Actions taken:**

1. Located Hogan Hogan in ADUC
2. Right-clicked account and selected Enable Account
3. Received AD confirmation: Object Hogan Hogan has been enabled
4. Verified account icon returned to normal active state

**Outcome:** Account fully reinstated. All settings, attributes, and group memberships intact. No data loss throughout the disable and re-enable cycle.

---

**Ticket closed. No further action required.**

*Documented by Nnamso Mkpong — IT Support Portfolio*
