# Install and Remove Business Software

> **Author:** Nnamso Mkpong
>
> **Domain:** Windows 11 - Software Deployment and Version Management
>
> **Environment:** Windows 11 Client, Adobe Acrobat Reader (Approved), Adobe Acrobat 5.0 (Legacy Conflict)
>
> **Completed:** April 2026

---

## Objective

Install an approved software package on a Windows 11 workstation, verify the correct version number, simulate a version conflict by installing a legacy application alongside it, identify the conflict using the Windows Program Compatibility Assistant and the Installed Apps list, remove the conflicting version using Apps and Features and confirm the approved version opens successfully without errors.

---

## Business Scenario

> **Ticket #0042 | Software Deployment — Approved Version Required Before Shift Start**
>
> A user needs a line-of-business application installed before the start of their shift. A previous installation is suspected on the machine and the IT department requires that the approved version is confirmed, any conflicting version is removed, and evidence of the correct state is captured before the user begins work.

This is a standard software deployment and remediation task. In practice it covers three separate failure modes that frequently occur together: the approved software was never installed, the approved software was installed but the wrong version is active, or a legacy version is present alongside the approved version and is creating a conflict. Knowing how to diagnose which of these applies and resolve it quickly is a core IT support competency.

---

## Environment and Tools Used

| Component | Detail |
|---|---|
| **Client OS** | Windows 11 |
| **Approved Application** | Adobe Acrobat Reader - Version 2025.001.21288 (64-bit) |
| **Conflicting Application** | Adobe Acrobat 5.0 / Acrobat Reader 5.1 (legacy, 2002) |
| **Tools Used** | File Explorer, Adobe Acrobat Installer, Settings → Apps → Installed Apps, Program Compatibility Assistant, Help → About |

---

## Steps Performed

---

### Phase 1 - Install the Approved Version

**Step 1.1 - Download the Approved Installer**

Navigate to the official Adobe download page and download the latest Adobe Acrobat Reader installer. Save it to the Downloads folder and confirm the file is present before running it.

<img width="1000" height="709" alt="01 Download installer" src="https://github.com/user-attachments/assets/80b70cae-ecc6-4a8c-b45f-2a6932ec88f1" />

> The installer is present in the Downloads folder and ready to run.
---

**Step 1.2 - Run the Installer and Monitor Progress**

Double-click the installer to launch it. The Adobe Acrobat Reader Installer window displays installation progress. Monitor it through to completion and do not interrupt it.

<img width="658" height="452" alt="02 Installation in progress" src="https://github.com/user-attachments/assets/afd2fa57-8555-497e-9384-543fd042d415" />


> The installation is running. The progress bar at 80% confirms the installer is active and writing files. This stage typically takes 1–3 minutes depending on the machine.

---

**Step 1.3 - Confirm Installation Complete**

Wait for the progress bar to reach 100% and the status message to change to "Installation complete."

<img width="668" height="454" alt="03 Installation complete" src="https://github.com/user-attachments/assets/f8b1b420-af0c-4c6b-bf41-766972ad85de" />


> Installation is confirmed complete. The checkmark icon and "Installation complete" message confirm the installer finished without error. The approved application is now present on the machine.

---

### Phase 2 - Verify the Version Number

**Step 2.1 - Confirm the Version via Help → About**

Open Adobe Acrobat Reader. Navigate to **Menu → Help → About Adobe Acrobat Reader** and confirm the version string matches the approved version required by IT.

<img width="581" height="511" alt="04 version check" src="https://github.com/user-attachments/assets/b9538f8a-8a5d-44b1-9cf1-f140d95f0a17" />


> **Highlighted:** The version string `2025.001.21288 | 64-bit` is confirmed. This is the approved version. String recorded  in the ticket note and in any software inventory log before closing this phase

---

### Phase 3 - Simulate the Version Conflict

**Step 3.1 - Download the Legacy Installer**

To simulate a real-world conflict, download Adobe Acrobat Reader 5.1 which is a legacy version from 2002. This represents a machine where a previous technician, a user, or a legacy system migration left an old version present alongside the new one.

<img width="1007" height="544" alt="05 Legacy installer " src="https://github.com/user-attachments/assets/fad7a096-074c-4742-a212-10858a57f137" />


> The legacy installer `acrobat51` (13,415 KB) is present in the Downloads folder. The file size difference from the approved installer (1.56 MB vs 13 MB) is also a useful indicator — full legacy installers are typically much larger than modern stub installers.

---

**Step 3.2 - Run the Legacy Installer**

Launch the legacy installer. The setup wizard uses the classic Windows XP-era interface, which is itself a diagnostic signal that this software predates modern Windows.

<img width="472" height="360" alt="06 Legacy wizard" src="https://github.com/user-attachments/assets/61e45777-0a6d-49d8-b426-51bcb865a488" />


> The setup wizard confirms this is **Reader 5.1**. In a real scenario, a technician would not install this. This step simulates what was already present on the machine before the support call.

---

**Step 3.3 - Legacy Installation Completes**

The legacy installer completes and presents a dialog confirming Acrobat Reader is installed.

<img width="298" height="146" alt="07 legacy install confimation" src="https://github.com/user-attachments/assets/05fed8fc-b723-4618-9d41-be727c556f37" />


> The confirmation dialog reads "Thank you for choosing Acrobat Reader." The legacy version is now present on the machine alongside the approved version.


---

**Step 3.4 - Confirm the Conflict State**

The legacy splash screen confirms the installed version is **Acrobat 5.1.0** from September 17, 2002. Both versions are now installed on the same Windows 11 machine simultaneously.

<img width="499" height="374" alt="08 Legacy splash screen" src="https://github.com/user-attachments/assets/2bb53df9-c4ba-48b3-a502-483bc0a489cb" />


> The splash screen date of **9/17/2002** confirms this is a 23-year-old application running on Windows 11. Two desktop icons are now present which is one for the approved version and one for the legacy version. The user may not know which one to open, or may be opening the wrong one habitually.

---

### Phase 4 - Identify the Conflict

**Step 4.1 - Observe the Windows Compatibility Warning**

When a user or technician attempts to open Adobe Acrobat 5 on Windows 11, the **Program Compatibility Assistant** fires automatically.

<img width="1022" height="716" alt="09 Compatibility Warning" src="https://github.com/user-attachments/assets/2b6060c2-d0c7-4f0f-8b3d-b342fbf2b537" />


> **Highlighted:**
> - The application title confirms **Adobe Acrobat 5** is the app generating the warning.
> - The warning body states that this app cannot run because it causes security or performance issues on Windows and that a new version may be available.
>
> This is the compatibility conflict. Windows 11 has identified that this application cannot run safely on the current OS.

This warning fires because:

- Adobe Acrobat 5 is a 32-bit legacy application built for Windows XP
- It uses system components and driver interfaces that Windows 11 no longer supports in the same way
- Windows has flagged it as a security or performance risk and is blocking execution

> The Program Compatibility Assistant is a diagnostic signal, not just a warning to dismiss. When it fires in a support scenario, it confirms the root cause of the user's problem and identifies exactly which application to remove.

---

### Phase 5 - Remove the Conflicting Version

**Step 5.1 - Uninstall via Settings → Apps → Installed Apps**

Navigate to **Settings → Apps → Installed Apps**. Locate **Adobe Acrobat 5.0** in the list. Click the three-dot menu (⋮) and select **Uninstall**.

<img width="775" height="722" alt="10 unistalllegacy version" src="https://github.com/user-attachments/assets/9b84e791-cc28-4ba7-a319-3b018e80cf40" />


> **Highlighted:**
> - **Adobe Acrobat 5.0** (dated 4/2/2026) is the conflicting version to be removed.
> - The **Uninstall** option is selected in the dropdown.
> - **Adobe Acrobat (64-bit) 26.001.21367** — the approved version — is visible at the top of the list and must not be touched.

> Always use Apps and Features or the system uninstaller to remove applications. Never delete application folders manually. Manual deletion leaves behind registry entries, file associations, and start menu shortcuts that can cause ongoing conflicts and break the Windows installer database for that application.

**Step 5.2 - Confirm Removal**

After the uninstaller completes, refresh the Installed Apps list and confirm Adobe Acrobat 5.0 no longer appears. Only the approved Adobe Acrobat (64-bit) entry should remain.

---

### Phase 6 - Confirm the Approved Version is Running

**Step 6.1 - Launch the Approved Version**

Open Adobe Acrobat Reader from the Start Menu or the desktop shortcut. Confirm it opens to the home screen without any Program Compatibility Assistant warning, error dialog, or application crash.

<img width="1027" height="766" alt="11 Approved version running" src="https://github.com/user-attachments/assets/51e0a951-6a7f-4721-bdd0-e02f10177a76" />

> **Highlighted:** The "Welcome to Acrobat Reader" home screen loads correctly. No compatibility warning appeared or error dialog appeared. The approved version is the only Adobe Reader present on the machine and it is functioning correctly.

**Step 6.2 - Reconfirm the Version**

As a final check, navigate to **Help → About** one more time and confirm the version string has not changed. This verifies that removing the legacy version did not affect the approved installation.

```
Adobe Acrobat Reader
Continuous Release | Version 2025.001.21288 | 64-bit
```

> The version string is unchanged. The approved version is installed, confirmed and running. The task is complete.

---

## Business Impact Note

> **Software version conflicts have a disproportionate impact relative to their technical complexity.**

When a user has the wrong version of a business application, or when a legacy version is interfering with the approved one, the impact is immediate. The user cannot complete their work using the correct tools, file compatibility issues may arise when documents are opened with an older application, and in regulated environments, running unapproved software versions is itself a compliance issue regardless of whether it causes a visible problem.

For a technician supporting a managed environment, the ability to confirm the approved version is installed, identify a conflicting version, remove it cleanly, and document the result in a ticket note is a baseline competency. The structured three-layer check — compatibility warning, installed apps audit, version confirmation — delivers a complete picture in under five minutes in most cases.

---

## Help Desk Ticket Note

See `TICKET-0042-software-deployment.md` in this folder.

---

## Outcome and Validation

| Check | Result |
|---|---|
| Approved installer downloaded from official source | Pass |
| Installation completed without error | Pass |
| Version confirmed via Help → About: 2025.001.21288 (64-bit) | Pass |
| Legacy version (Acrobat 5.1) installed to simulate conflict | Pass |
| Conflict state confirmed: Program Compatibility Assistant warning fires | Pass |
| Legacy version identified in Installed Apps list | Pass |
| Legacy version removed via Settings → Apps → Uninstall | Pass |
| Approved version confirmed running after legacy removal | Pass |
| Version string reconfirmed after legacy removal — unchanged | Pass |

---

## What I Learned

1. **Version confirmation is not optional after installation.** The installer completing without error does not guarantee the correct version was deployed. Checking Help → About immediately after installation takes 10 seconds and closes a category of error that would otherwise only surface when the user tries to open a specific file type or feature.

2. **Multiple versions of the same application can coexist silently.** Windows does not prevent a legacy version from being installed alongside a current version in most cases. The conflict only becomes visible when the user tries to open the legacy application, when file associations are contested, or when the Program Compatibility Assistant fires. A technician who does not audit the Installed Apps list will miss this.

3. **The Program Compatibility Assistant is a diagnostic tool, not a nuisance.** When it fires, it is providing precise information: the application named in the dialog cannot run safely on this OS. That is the application to remove. Dismissing the warning without reading it is a missed diagnostic opportunity.

4. **Always use the system uninstaller.** Apps and Features and the Control Panel Programs list both trigger the application's own uninstaller, which removes registry entries, file associations, and system components correctly. Deleting folders bypasses this and leaves behind residual configuration that can cause future conflicts.

5. **Documentation is part of the job, not an optional add-on.** In a managed environment, software changes need to be recorded so that other technicians can understand the current state of the machine, audits can be passed, and recurring issues can be traced to their root cause. A ticket note that records what was found, what was changed, and what was confirmed is the minimum acceptable output for any software deployment task.

---

## Real World Relevance

Software version management is one of the most common categories of help desk work in any organisation using a managed Windows environment. Users may have legacy applications left over from system migrations, self-installed personal versions that conflict with IT-approved software, or packages that were deployed correctly but never updated after a company-wide version change.

A technician who approaches software deployment with a structured diagnostic mindset — confirm what is installed, verify the version, identify and remove conflicts, confirm the resolved state — will resolve these calls quickly and create a clean audit trail. A technician who assumes that because the application opens it is the correct version, or who reinstalls without first auditing what is already present, will create additional problems and generate follow-up tickets.

Software deployment and version management also appears consistently in technical interview scenarios for help desk and desktop support roles. Candidates who can describe the relationship between the Installed Apps list, the Program Compatibility Assistant, and the Help → About version check demonstrate that they think in layers and understand why each step matters — which is the diagnostic mindset employers in IT support are looking for.

---

## Troubleshooting Reference

| Symptom | Likely Cause | Resolution |
|---|---|---|
| Application opens but wrong features available | Wrong version installed | Help → About to confirm; reinstall approved version |
| Program Compatibility Assistant fires on launch | Legacy application incompatible with Windows 11 | Installed Apps → Uninstall the legacy version |
| Two desktop icons for the same application | Multiple versions installed simultaneously | Audit Installed Apps; remove the unapproved version |
| Application fails to open silently | Corrupted installation or missing dependency | Reinstall from clean approved installer |
| File opens in wrong application | File association set to legacy version | Default Apps settings; remove legacy version |
| Uninstall option greyed out in Installed Apps | Insufficient permissions or corrupted installer database | Run uninstall as administrator; use vendor removal tool if needed |
| Application reinstalls to old version automatically | Auto-update disabled or update source pointing to wrong version | Check update settings; confirm installer source URL |
