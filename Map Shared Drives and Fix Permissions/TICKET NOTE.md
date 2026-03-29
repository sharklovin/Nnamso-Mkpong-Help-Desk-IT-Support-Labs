# Help Desk Ticket #0053

**Category:** Shared Drive Access - Permissions Fault  
**Priority:** Standard  
**Status:** Resolved  

---

## Ticket Details

| Field | Value |
|------|------|
| Ticket ID | 0053 |
| Date Opened | March 2026 |
| Date Resolved | March 2026 |
| Assigned To | Nnamso Mkpong – IT Support |
| Share Name | DepartmentShare |
| Drive Letter | Z: |
| Server | [DC_IP] |
| Domain | [DOMAIN] |

---

## Issue Reported

User **Otobong Mkpong (Choko101@[DOMAIN])** reported being unable to access the shared folder:

\\[DC_NAME]\DepartmentShare

The following error message was displayed:

"You do not have permission to access this network resource"

Another user **Jayson Sule (Jayson419@[DOMAIN])** confirmed successful access to the same share.

---

## Investigation

1. Verified the shared folder existed on the file server.
2. Confirmed Advanced Sharing was enabled.
3. Checked Share Permissions:
   - Department_Sales group had Change and Read permissions.
4. Checked NTFS Permissions:
   - Department_Sales group had Read, Write, and Read & Execute permissions.
5. Verified user access:
   - Jayson Sule → Member of Department_Sales (Access Working)
   - Otobong Mkpong → Not member of Department_Sales (Access Failed)

**Root Cause Identified:**  
User **Otobong Mkpong** was not part of the **Department_Sales** security group.

---

## Actions Taken

1. Created DepartmentShare folder on server.
2. Enabled Advanced Sharing with share name DepartmentShare.
3. Configured Share Permissions:
   - Department_Sales → Change and Read
4. Configured NTFS Permissions:
   - Department_Sales → Modify, Read, Write, Read & Execute
5. Created user Jayson Sule
6. Created user Otobong Mkpong
7. Added Jayson Sule to Department_Sales group
8. Left Otobong Mkpong out of group to reproduce issue
9. Mapped network drive:

Z: → \\[DC_NAME]\DepartmentShare

10. Tested access with Jayson Sule → Access Granted
11. Created test file to validate write access
12. Tested access with Otobong Mkpong → Access Denied
13. Added Otobong Mkpong to Department_Sales group
14. Requested user logoff and login
15. Retested access → Access Restored

---

## Outcome

Access successfully restored for **Otobong Mkpong** after adding the user to the **Department_Sales** security group.

No changes were required to:

- Share permissions  
- NTFS permissions  

Root cause was confirmed as **missing group membership**.

---

## Validation Evidence

- Authorized user successfully accessed shared drive  
- Unauthorized user received access denied  
- Group membership added  
- Access restored after logoff/login  
- File creation test successful  

---

## Troubleshooting Logic

| Check | Result |
|------|------|
| Share exists | Pass |
| Share permissions | Pass |
| NTFS permissions | Pass |
| Network connectivity | Pass |
| Group membership | Failed |
| Resolution | Add user to group |

---

## Real World Relevance

Shared drive permission issues are among the most common Help Desk tickets.  
This lab demonstrates:

- Active Directory troubleshooting  
- Share permission validation  
- NTFS permission troubleshooting  
- Group based access control  
- Real world help desk workflow  

---

## Ticket Closure Note

Department share access restored for affected user. Root cause identified as missing group membership in **Department_Sales**. Issue resolved without modifying existing permissions.

**Documented by:**  
Nnamso Mkpong  
IT Support Portfolio
