# Join a Windows 11 Device to an Active Directory Domain

> **Author:** Nnamso Mkpong
>
> **Domain:** Active Directory Administration
>
> **Environment:** Windows Server 2022 + Windows 11 Client (Virtualised)
> **Completed:** March 2026

---

## Objective

Join a Windows 11 workstation to an Active Directory domain, verify that a domain user can authenticate on the machine and confirm that the computer object is visible inside Active Directory Users and Computers.

---

## Business Scenario

> **Ticket #0052 | New Device Provisioning — Domain Join**
>
> A new laptop has arrived and must be prepared before the employee's first day. The device is currently in a workgroup and cannot authenticate against the corporate directory. IT support has been asked to join the machine to the **mylab.local** domain, confirm that a domain user can log in successfully, and verify that the computer account has been registered in Active Directory.

This scenario covers one of the most routine provisioning tasks in any IT support role: taking a fresh or reimaged device and bringing it into the domain so the end user can log in with their corporate credentials from day one.

---

## Environment and Tools Used

| Component | Detail |
|---|---|
| **Client OS** | Windows 11 |
| **Server OS** | Windows Server 2022 Evaluation |
| **Domain** | mylab.local |
| **Client Hostname** | WIN11_CLIENT01 |
| **Domain Controller IP** | 192.168.1.10 |
| **Tool** | System Properties (sysdm.cpl), Windows Settings, Command Prompt |
| **Admin Account Used** | Administrator@mylab.local |

---

## Lab Architecture

```
mylab.local
│
├── Domain Controllers
│     └── WIN-SERVER01 
│
├── Computers
│     └── WIN11_CLIENT01   ← Computer account created in this lab
│
└── Office Users
      └── (domain user accounts)
```

---

## Why DNS Must Point to the Domain Controller

> **This is the most important concept in this lab and the number one reason domain joins fail in real environments.**

Active Directory does not locate the domain by IP address. When a Windows machine attempts to join, it performs a **DNS lookup** for the domain name and specifically queries for **SRV records** that advertise the location of Kerberos and LDAP services on the Domain Controller.

If the client's DNS is pointed at a public resolver such as `8.8.8.8` or `1.1.1.1`, that server has no knowledge of your internal `mylab.local` zone. It returns a failure. The domain join never reaches the Domain Controller.

**Correct DNS chain:**

```
Windows 11 Client
      │
      │  "Where is mylab.local?"
      ▼
Domain Controller 
      │
      │  "Here are the SRV records. LDAP is on port 389. Kerberos on port 88."
      ▼
Client contacts DC → Credentials accepted → Domain join succeeds ✅
```

**Without DC as DNS:**

```
Windows 11 Client
      │
      │  "Where is mylab.local?"
      ▼
Public DNS — 8.8.8.8
      │
      │  "Never heard of mylab.local."
      ▼
Client cannot find DC → Domain join fails ❌
```

Setting DNS manually to the DC IP before any other step is not optional. It is the prerequisite on which everything else depends.

---

## Steps Performed

---

### Phase 1 — Configure DNS on the Client Machine

**Step 1.1 — Open DNS Settings and Switch from Automatic to Manual**

Open **Settings → Network and Internet → Ethernet → DNS server assignment** and click **Edit**. The default configuration shows **Automatic (DHCP)**, which pulls DNS from the router. This must be changed to **Manual** before a domain join can succeed.

<img width="503" height="226" alt="01-dns-settings-dhcp-vs-manual" src="https://github.com/user-attachments/assets/7120f1b9-4b1f-4510-a568-702593b709c7" />



> The DHCP-assigned DNS typically points to your home router or ISP. Neither of those resolvers knows about `mylab.local`. Switching to Manual gives you direct control over which DNS server the machine queries.

---

**Step 1.2 — Enter the Domain Controller IP as the Preferred DNS Server**

After selecting Manual, enable **IPv4** and enter the Domain Controller's IP address in the **Preferred DNS** field. Leave Alternate DNS blank. Click **Save**.

<img width="500" alt="Preferred DNS manually set to 192.168.1.10" src="screenshots/02-dns-set-to-domain-controller.png" />

> In this lab the DC IP is `192.168.1.10`. In a real environment this value comes from your network documentation or from the server team. Do not guess — an incorrect DNS entry is the single fastest way to break a domain join.

---

### Phase 2 — Verify DNS Resolution Before Joining

**Step 2.1 — Run nslookup to Confirm the Domain Resolves**

Before touching System Properties, open **Command Prompt as Administrator** and run the following:

```cmd
nslookup mylab.local 192.168.1.10
```

This queries the DC directly and asks it to resolve `mylab.local`. If DNS is correctly configured, the domain name resolves to the DC's IP address.

<img width="708" alt="nslookup confirming mylab.local resolves via the DC" src="screenshots/03-nslookup-verification.png" />

| Output field | Expected value | What it confirms |
|---|---|---|
| Server | Unknown (or DC hostname) | The DC responded to the query |
| Address | 192.168.1.10 | You queried the correct DNS server |
| Name | mylab.local | The domain name resolved successfully |
| Addresses | IPv4 and IPv6 of the DC | The DC is reachable |

> ✅ If `Name: mylab.local` resolves to the DC IP, DNS is ready and you can proceed.
>
> ❌ If you see `Non-existent domain`, DNS is still wrong. Go back to Step 1.2 and verify the saved DNS setting. Do not continue until this resolves correctly.

---

### Phase 3 — Join the Machine to the Domain

**Step 3.1 — Open System Properties**

Press `Win + R`, type `sysdm.cpl` and press Enter. This opens System Properties directly to the Computer Name tab, which is faster than navigating through Settings.

On the **Computer Name** tab, click **Change**.

---

**Step 3.2 — Select Domain and Enter the Domain Name**

In the **Computer Name/Domain Changes** dialog:

1. Under **Member of**, select **Domain** instead of Workgroup
2. Type `mylab.local` in the domain field
3. Click **OK**

<img width="322" alt="Computer Name/Domain Changes dialog with mylab.local entered" src="screenshots/04-joining-domain.png" />

> You will be prompted immediately for credentials. Enter a domain admin account — in this lab that is `Administrator`. A standard user account does not have permission to join computers to the domain by default.

---

**Step 3.3 — Confirm the Welcome Dialog**

If DNS is correct and the credentials are accepted, Active Directory processes the join and displays a confirmation:

> *"Welcome to the mylab.local domain."*

<img width="322" alt="Welcome to the mylab.local domain confirmation dialog" src="screenshots/05-domain-join-success.png" />

Click **OK** → click **OK** again on System Properties → click **Restart Now** when prompted.

> The restart is required. Group Policy does not apply, and domain logins do not work until the machine has rebooted into the domain context and established its secure channel with the DC.

---

### Phase 4 — Log In with a Domain Account

**Step 4.1 — Select Other User at the Login Screen**

After the machine restarts, at the Windows login screen click **Other user** in the bottom-left corner. This switches the login prompt from the local account to a domain credential entry.

Enter the domain account in UPN format and confirm the footer reads **Sign in to: mylab.local**.

<img width="845" alt="Windows login screen showing domain user login for mylab.local" src="screenshots/07-domain-user-login.png" />

| Login format | Example | Notes |
|---|---|---|
| UPN format (recommended) | `Administrator@mylab.local` | Clearer and less ambiguous |
| NetBIOS format | `mylab\Administrator` | Legacy format, still valid |

> On the very first login for a domain user, Windows creates a local profile for that account under `C:\Users\`. This takes 15 to 30 seconds and is completely normal behaviour. The user will land on the desktop once the profile is built.

---

### Phase 5 — Verify the Computer Object in Active Directory

**Step 5.1 — Open Active Directory Users and Computers on the DC**

On the Domain Controller, open **Server Manager → Tools → Active Directory Users and Computers**.

Navigate to: `mylab.local → Computers`

**WIN11_CLIENT01** should now appear as a computer object in this container.

<img width="845" alt="WIN11_CLIENT01 visible in Active Directory Computers OU" src="screenshots/06-computer-in-active-directory.png" />

> The computer object in Active Directory is not just a visual confirmation. It is the machine's identity in the domain. It holds the secure channel password, enables Group Policy targeting, and is the basis for remote management tools such as SCCM and Remote Desktop. No computer object means no domain membership, even if the client-side settings appear correct.

---

## Help Desk Ticket Note

```
──────────────────────────────────────────────────────────────────
TICKET #0052 | NEW DEVICE PROVISIONING — DOMAIN JOIN
Technician: Nnamso Mkpong | Date: March 2026 | Priority: STANDARD
──────────────────────────────────────────────────────────────────

ISSUE REPORTED:
  New laptop WIN11_CLIENT01 received for employee. Device is in
  WORKGROUP state and cannot authenticate against the domain.
  IT Support requested to domain-join the device and confirm
  domain login works before handing off to the end user.

ACTIONS TAKEN:
  1. Opened Network Settings on WIN11_CLIENT01.
  2. Changed DNS from Automatic (DHCP) to Manual.
  3. Set Preferred DNS to 192.168.1.10 (Domain Controller IP).
  4. Ran nslookup mylab.local 192.168.1.10 to confirm resolution.
  5. Opened sysdm.cpl → Computer Name tab → Change.
  6. Selected Domain, entered mylab.local, clicked OK.
  7. Authenticated with Administrator@mylab.local credentials.
  8. Received "Welcome to the mylab.local domain" confirmation.
  9. Restarted the client machine as prompted.
  10. At login screen selected Other User.
  11. Logged in as Administrator@mylab.local — login successful.
  12. Verified WIN11_CLIENT01 appears in ADUC → Computers OU.

OUTCOME:
  Device successfully joined to mylab.local domain. Domain user
  authenticated on first attempt. Computer account confirmed in
  Active Directory. Device ready for end user handoff.
──────────────────────────────────────────────────────────────────
```

---

## Outcome and Validation

| Check | Result |
|---|---|
| DNS manually set to Domain Controller IP 192.168.1.10 | Pass |
| nslookup confirms mylab.local resolves via the DC | Pass |
| Computer Name/Domain Changes dialog shows mylab.local entered | Pass |
| "Welcome to the mylab.local domain" confirmation received | Pass |
| Machine restarted and domain login screen presented | Pass |
| Domain user authenticated successfully on first login | Pass |
| WIN11_CLIENT01 visible in ADUC under Computers OU | Pass |

---

## What I Learned

1. **DNS is the foundation of every Active Directory operation.** The domain join process does not work by IP address. It resolves the domain through DNS SRV records that only the Domain Controller knows about. Getting DNS wrong is the number one cause of domain join failures, and fixing it is always the first diagnostic step.

2. **Verify before you act.** Running `nslookup` before opening System Properties takes 10 seconds and confirms that DNS is correctly configured. This prevents wasted time troubleshooting a failed join when the root cause is still sitting in the DNS settings.

3. **`sysdm.cpl` is faster than the Settings app.** Experienced technicians use the Run dialog to reach System Properties directly. Learning keyboard shortcuts and direct console access speeds up every provisioning task considerably.

4. **The computer object in AD is the machine's domain identity.** Joining the domain does not just change a setting on the client. It creates a computer account in Active Directory that holds group policy targeting data, a rotating machine password, and the basis for remote management. This object is what makes the device a managed, trusted member of the domain.

5. **First domain login creates the local profile.** When a domain user logs in for the first time, Windows builds a new profile directory under `C:\Users\`. Knowing this prevents unnecessary follow-up calls when users see a brief delay on their first login.

---

## Real World Relevance

Joining a device to the domain is one of the most frequently performed provisioning tasks in any organisation that runs Active Directory. Every new starter, every reimaged machine, and every replacement device goes through this process before it is handed to the end user.

The DNS step is where junior technicians most commonly get stuck. A machine that cannot resolve the domain name generates cryptic error messages that seem unrelated to DNS, and without the background knowledge to recognise it, a technician can spend significant time troubleshooting the wrong thing. Understanding that Active Directory is entirely dependent on DNS — and knowing to check that first — is a hallmark of a technician who can work efficiently and independently.

Beyond the initial join, the computer object created in Active Directory is what enables Group Policy enforcement, software deployment via SCCM or Intune, and remote support tools. A device that is not properly domain-joined cannot receive any of those. The domain join is not just a login prerequisite: it is the entry point for the entire managed device lifecycle. That is why it matters, and that is why doing it correctly from the start saves time at every stage that follows.

---

## Troubleshooting Reference

| Symptom | Likely Cause | Resolution |
|---|---|---|
| "The domain could not be found" | DNS still pointing to router or public resolver | Set Preferred DNS to the DC IP and re-test with nslookup |
| "The specified domain does not exist or could not be contacted" | Domain name typed incorrectly | Confirm spelling of mylab.local and retry |
| "Access is denied" during join | Wrong credentials or insufficient permissions | Use a domain admin account; verify username and password |
| nslookup returns "Non-existent domain" | DNS not reaching the DC | Ping 192.168.1.10 to check basic connectivity first |
| Computer not appearing in ADUC after join | ADUC view needs refreshing | Press F5 to refresh; allow 30 seconds for replication |
| Domain login fails after restart | Secure channel not established correctly | Re-check the join completed without errors; retry if needed |
| First login takes unusually long | Profile creation on first domain login | This is normal; wait for the profile to finish building |
| "Clock skew too great" Kerberos error | Client clock differs from DC by more than 5 minutes | Sync the client clock to the DC or an NTP source |
