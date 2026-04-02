# Lab 12: Install and Remove Business Software

> **Course:** IT Support & Help Desk Fundamentals  
> **Lab Number:** 12  
> **Difficulty:** Beginner–Intermediate  
> **Estimated Time:** 30–45 minutes  
> **Platform:** Windows 11

---

## Table of Contents

- [Objective](#objective)
- [Business Scenario](#business-scenario)
- [Tools Used](#tools-used)
- [Prerequisites](#prerequisites)
- [Steps Performed](#steps-performed)
  - [Step 1 – Download the Approved Installer](#step-1--download-the-approved-installer)
  - [Step 2 – Install the Approved Version](#step-2--install-the-approved-version)
  - [Step 3 – Verify the Version Number](#step-3--verify-the-version-number)
  - [Step 4 – Simulate a Version Conflict](#step-4--simulate-a-version-conflict)
  - [Step 5 – Identify the Conflict](#step-5--identify-the-conflict)
  - [Step 6 – Remove the Conflicting Version](#step-6--remove-the-conflicting-version)
  - [Step 7 – Confirm the Approved Version Works](#step-7--confirm-the-approved-version-works)
- [Outcome](#outcome)
- [What I Learned](#what-i-learned)
- [Real-World Relevance](#real-world-relevance)
- [Ticket Note](#ticket-note)
- [File Structure](#file-structure)

---

## Objective

Install an approved software package on a Windows 11 machine, simulate a version conflict by installing an older version alongside it, then remove the conflicting version using **Apps & Features**. Verify the correct approved version is running at the end.

---

## Business Scenario

> A user needs a line-of-business application installed before the start of a shift. A previous technician had installed an outdated version that is no longer approved by the IT department. The task is to install the correct version, identify the conflict, remove the legacy version, and confirm the approved application opens without error.

**Application chosen for this lab:** Adobe Acrobat Reader (latest) vs. Adobe Acrobat Reader 5.1 (legacy conflict simulation)

---

## Tools Used

| Tool | Purpose |
|---|---|
| Windows 11 | Host operating system |
| File Explorer – Downloads | Locating installer packages |
| Adobe Acrobat Reader Installer | Installing the approved version |
| Acrobat Reader 5.1 Installer | Simulating the legacy/conflict version |
| Settings → Apps → Installed Apps | Uninstalling the conflicting version |
| Program Compatibility Assistant | Identifying compatibility conflicts |
| Help → About (in-app) | Confirming the installed version number |

---

## Prerequisites

- A Windows 11 machine (physical or VM)
- Internet access to download installer packages
- Administrator privileges on the machine
- Basic familiarity with File Explorer and Windows Settings

---

## Steps Performed

---

### Step 1 – Download the Approved Installer

Navigate to the official Adobe download page and download the latest **Adobe Acrobat Reader** installer. Save it to your **Downloads** folder.

![Step 1 – Installer downloaded to Downloads folder](screenshots/01_download_installer.png)

> **What to note:** The installer filename is `Reader_en_install` and the file size is approximately **1.56 MB**. Always verify you are downloading from the official vendor site to avoid supply-chain risks.

---

### Step 2 – Install the Approved Version

Double-click the installer to launch it. The Adobe Acrobat Reader Installer window will appear showing installation progress.

**Screenshot 2a – Installation in progress (80%):**

![Step 2a – Installation in progress](screenshots/02_installation_in_progress.png)

**Screenshot 2b – Installation complete (100%):**

![Step 2b – Installation complete](screenshots/03_installation_complete.png)

> **What to note:** Wait for the progress bar to reach **100%** and the status to read *"Installation complete"* before proceeding. Do not interrupt the installer.

---

### Step 3 – Verify the Version Number

Once installed, open Adobe Acrobat Reader and navigate to:  
**Menu → Help → About Adobe Acrobat Reader**

![Step 3 – Version check showing 2025.001.21288 (64-bit)](screenshots/04_version_check_annotated.png)

> **✅ Highlighted:** The version string `2025.001.21288 | 64-bit` confirms the approved version is installed. Record this for your audit log and ticket note.

**Version confirmed:**

```
Adobe Acrobat Reader
Continuous Release | Version 2025.001.21288 | 64-bit
```

---

### Step 4 – Simulate a Version Conflict

To simulate a real-world conflict scenario, download and install **Adobe Acrobat Reader 5.1** (a legacy version from 2002). Save the installer `acrobat51` to your Downloads folder.

**Screenshot 4a – Legacy installer in Downloads folder:**

![Step 4a – Legacy acrobat51 installer](screenshots/05_download_older_version.png)

**Screenshot 4b – Legacy version Setup Wizard:**

![Step 4b – Acrobat Reader 5.1 setup wizard](screenshots/06_older_version_setup_wizard.png)

> **What to note:** The Setup Wizard for version 5.1 uses a classic Windows XP-era interface. This is a strong visual indicator that the software is legacy and not suited for a modern OS environment.

**Screenshot 4c – Legacy installation complete:**

![Step 4c – Legacy install complete confirmation](screenshots/07_older_version_install_complete.png)

**Screenshot 4d – Legacy version splash/about screen:**

![Step 4d – Acrobat Reader 5.1 splash screen](screenshots/08_older_version_splash.png)

> **What to note:** The splash screen confirms **Acrobat 5.1.0 – September 17, 2002**. Both versions are now installed on the same machine — this creates the conflict condition.

---

### Step 5 – Identify the Conflict

When attempting to open **Adobe Acrobat 5**, Windows 11 displays a **Program Compatibility Assistant** warning.

![Step 5 – Compatibility conflict warning](screenshots/09_compatibility_conflict_warning_annotated.png)

> **⚠️ Highlighted:** The warning states: *"This app can't run because it causes security or performance issues on Windows. A new version may be available."*  
>
> This is the compatibility conflict. Adobe Acrobat 5 is a 32-bit legacy application that Windows 11 flags as a security/performance risk. This is the trigger to remove it.

---

### Step 6 – Remove the Conflicting Version

Navigate to:  
**Settings → Apps → Installed Apps**

Locate **Adobe Acrobat 5.0** in the list. Click the **three-dot menu (⋮)** next to it and select **Uninstall**.

![Step 6 – Uninstalling Adobe Acrobat 5.0 via Installed Apps](screenshots/10_uninstall_old_version_annotated.png)

> **✅ Highlighted:**
> - **Adobe Acrobat (64-bit) 26.001.21367** — the approved version — is visible at the top and should be kept.
> - **Adobe Acrobat 5.0** is the conflicting version being removed.
> - The **Uninstall** option is visible in the dropdown context menu.

**Verification:** After uninstalling, refresh the Installed Apps list and confirm Adobe Acrobat 5.0 no longer appears.

---

### Step 7 – Confirm the Approved Version Works

After removing the legacy version, launch **Adobe Acrobat Reader** from the Start Menu or desktop shortcut. Confirm it opens to the home screen without any error.

![Step 7 – Approved version opens successfully](screenshots/11_approved_version_confirmed_annotated.png)

> **✅ Highlighted:** The "Welcome to Acrobat Reader" home screen loads correctly with no compatibility warnings. The approved version is the only Adobe Reader present on the system.

---

## Outcome

| Check | Result |
|---|---|
| Approved version installed | ✅ Adobe Acrobat Reader 2025.001.21288 (64-bit) |
| Version number verified via Help → About | ✅ Confirmed |
| Legacy version (5.1) installed to simulate conflict | ✅ Done |
| Compatibility conflict identified | ✅ Windows Program Compatibility Assistant warning |
| Legacy version removed via Apps & Features | ✅ Adobe Acrobat 5.0 uninstalled |
| Approved version confirmed working after removal | ✅ Opens successfully with no errors |

**The lab was completed successfully.** The approved version of Adobe Acrobat Reader is the only version installed, and it opens without any warnings or errors.

---

## What I Learned

1. **Always verify version numbers after installation** — using Help → About is the standard method; never assume the correct version installed just because it didn't error out.

2. **Multiple versions of the same software can coexist and conflict** — modern Windows may silently allow installation of a legacy version without blocking it, but conflicts surface at runtime.

3. **Windows Program Compatibility Assistant is a diagnostic tool** — when it fires, it is evidence of a version or OS compatibility issue, not just a generic warning to dismiss.

4. **Apps & Features (Settings) is the correct uninstall path** — never delete application folders manually; always use the proper uninstaller to remove registry entries and system files cleanly.

5. **Documenting version strings for audit trails matters** — in a business environment, software versions are tied to licensing, security patches, and support contracts; recording them is not optional.

---

## Real-World Relevance

In a real help desk or IT support environment, software version management is a daily responsibility. Users may have legacy software left over from previous technicians, self-installed personal versions, or packages that were never updated after a company-wide rollout. A technician who installs software without verifying the version, or who fails to remove a conflicting version, can create downstream problems such as file association conflicts (two apps both claiming to open `.pdf` files), security vulnerabilities from unpatched legacy software, licensing violations if an unapproved version is present, and application crashes that generate unnecessary tickets.

This lab builds the habit of **install → verify → document**, which is the minimum acceptable standard for software deployment in any managed IT environment.

---

## Ticket Note

See [`TICKET_NOTE.md`](TICKET_NOTE.md) for the formal support ticket note written to accompany this work.

---

## File Structure

```
lab-12-install-remove-software/
│
├── README.md                          ← This file (full lab documentation)
├── TICKET_NOTE.md                     ← Formal IT support ticket note
│
└── screenshots/
    ├── 01_download_installer.png
    ├── 02_installation_in_progress.png
    ├── 03_installation_complete.png
    ├── 04_version_check.png
    ├── 04_version_check_annotated.png          ← ✏️ Annotated
    ├── 05_download_older_version.png
    ├── 06_older_version_setup_wizard.png
    ├── 07_older_version_install_complete.png
    ├── 08_older_version_splash.png
    ├── 09_compatibility_conflict_warning.png
    ├── 09_compatibility_conflict_warning_annotated.png  ← ✏️ Annotated
    ├── 10_uninstall_old_version.png
    ├── 10_uninstall_old_version_annotated.png   ← ✏️ Annotated
    ├── 11_approved_version_confirmed.png
    └── 11_approved_version_confirmed_annotated.png      ← ✏️ Annotated
```

---

*Lab completed by: [Your Name] | Date: April 2, 2026 | Environment: Windows 11 VM*
