# Help Desk Ticket #0052

**Category:** New Device Provisioning - Domain Join  
**Priority:** Standard  
**Status:** Resolved

---

## Ticket Details

| Field | Value |
|---|---|
| Ticket ID | 0052 |
| Date Opened | March 2026 |
| Date Resolved | March 2026 |
| Assigned To | Nnamso Mkpong - IT Support |
| Device Name | WIN11_CLIENT01 |
| Domain | [DOMAIN] |
| Domain Controller IP | [DC_IP] |
| Authentication Account Used | Administrator@[DOMAIN] |

---

## Issue Reported

A new laptop named **WIN11_CLIENT01** was received for employee use. The device was still in **WORKGROUP** state and could not authenticate against the domain. IT Support was asked to join the laptop to the **mylab.local** domain and confirm that domain authentication worked successfully before handing the device over to the end user.

---

## Actions Taken

1. Opened **Network Settings** on **WIN11_CLIENT01**.
2. Changed DNS from **Automatic (DHCP)** to **Manual**.
3. Set the preferred DNS server to **[DC_IP]**, the Domain Controller IP.
4. Ran `nslookup [DOMAIN] [DC_IP]` to confirm that the domain resolved successfully through the domain controller.
5. Opened `sysdm.cpl`, accessed the **Computer Name** tab, and selected **Change**.
6. Selected **Domain**, entered **[DOMAIN]**, and submitted the request.
7. Authenticated with **Administrator@[DOMAIN]** credentials.
8. Received the confirmation message: **"Welcome to the [DOMAIN] domain."**
9. Restarted the client machine as prompted to complete the domain join.
10. At the login screen, selected **Other user**.
11. Logged in with **Administrator@[DOMAIN]** successfully to confirm domain authentication.
12. Verified that **WIN11_CLIENT01** appeared in **Active Directory Users and Computers** under the **Computers** container.

---

## Outcome

The device was successfully joined to **[DOMAIN]**. Domain authentication worked as expected after restart, and the computer account was confirmed in Active Directory. The laptop is now ready for end user handoff.

---

## Validation Evidence

- DNS resolution to the domain controller completed successfully
- Domain join confirmation message was received
- Restart completed without errors
- Domain login succeeded
- Computer object appeared in Active Directory

---

## Ticket Closure Note

Domain join completed successfully for **WIN11_CLIENT01**. Device can now authenticate against **[DOMAIN]** and is ready for deployment to the assigned user.

*Documented by Nnamso Mkpong - IT Support Portfolio*
