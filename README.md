# Windows Server Infrastructure & Active Directory Administration
> **Status:** Portfolio Complete — v1.0
> **Last Updated:** June 2026

### Active Directory · DNS · Group Policy · Domain Controller Promotion · Windows Server 2022

**Md Rahat Islam Anik · Systems Administrator · Cloud & Infrastructure Operations · 2026**

[![GitHub Repo](https://img.shields.io/badge/GitHub-Repository-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/rahatislamanik-spec/Windows-Server-Infrastructure-Active-Directory)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-rahatislamanik-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/rahatislamanik)

---

## Overview

This repository documents the hands-on deployment of a Windows Server 2022 Active Directory environment — from OS installation through domain controller promotion and first domain authentication.

Every configuration step is captured in sequence: server provisioning, AD DS role installation, the full DC promotion wizard, DNS integration, and verified domain logon. The focus here is the deployment process itself — what you actually do when you build an AD forest from scratch, and why each step matters.

This repository records a focused deployment exercise using the `anik.local` lab forest. The companion project below documents a separate, broader administration lab using `lab.local`; it is cross-referenced for complementary operational evidence, not presented as the same forest:

> **Related project: [Windows-Server-2022-Enterprise-Domain](https://github.com/rahatislamanik-spec/Windows-Server-2022-Enterprise-Domain)** — full enterprise domain lab with 158 screenshots covering DHCP, DNS, Group Policy, file services, and end-to-end workstation validation.

---

## Architecture

![Windows Server 2022 Domain Controller Architecture](assets/windows-server-2022-domain-controller-architecture-v2.svg)

The architecture presents the target operating model around the deployed AD DS and DNS foundation. This repository's retained screenshots directly evidence Windows Server installation, AD DS role installation, forest promotion, DNS integration, and first domain authentication. DHCP, OU, security-group, GPO, and client-system elements show the intended extension path and are not claimed here as retained implementation evidence.

---

## Environment

| Component | Details |
|---|---|
| Domain | anik.local |
| Domain Controller | Windows Server 2022 Datacenter Evaluation |
| Virtualization | UTM (QEMU/x86_64) on macOS |
| Services Deployed | AD DS, DNS |
| Evidence Scope | OS installation, role installation, forest promotion, DNS integration, first domain authentication |
| Management Tools | Server Manager, ADUC, GPMC, DNS Manager |

> **Namespace note:** `anik.local` is a lab-only namespace retained in historical evidence. A production design should use a registered DNS subdomain such as `ad.example.com` rather than `.local`.

---

## What Was Built

### OS Installation & Initial Configuration

Windows Server 2022 Datacenter Evaluation (Desktop Experience) was installed on a virtualized x86_64 environment — 4 vCPUs, 8 GB RAM, 64 GB storage. The built-in Administrator account was configured and Server Manager confirmed loaded on first login before any roles were added.

### AD DS Role Installation

The Active Directory Domain Services role was installed through the Add Roles and Features Wizard in Server Manager, along with Group Policy Management and Remote Server Administration Tools. Each selection was confirmed before proceeding — role installation and DC promotion are two separate steps, and keeping them separate makes it easier to catch configuration issues early.

### Domain Controller Promotion

The server was promoted to Domain Controller using the AD DS Configuration Wizard. A new forest was created with root domain `anik.local`. Key decisions made during promotion:

- DNS Server and Global Catalog enabled — both required for a first DC in a new forest
- DSRM password configured — essential for directory restore scenarios
- DNS delegation skipped — expected and correct for a standalone lab environment
- NetBIOS name set to `ANIK` automatically
- Database, log, and SYSVOL paths left at defaults: `C:\Windows\NTDS` and `C:\Windows\SYSVOL`
- All prerequisites passed before installation began

The wizard generated an `Install-ADDSForest` PowerShell script capturing the full configuration — useful for repeatable deployments or documenting exactly what was applied.

### DNS Integration

DNS was configured as the authoritative resolver for `anik.local` at promotion. This is not optional — Active Directory cannot function without DNS. Domain join operations, Kerberos authentication, replication between domain controllers, and SRV records all depend on DNS being correctly configured before the first client touches the domain.

### First Domain Logon

After promotion and automatic reboot, the server authenticated as `ANIK\Administrator`. This confirms that promotion completed and domain authentication was available on the new controller; it does not by itself prove full domain-controller health, replication, backup, monitoring, or production readiness.

---

## Target Directory Design

### Organizational Unit Structure

The following OU hierarchy is the target design for a fuller implementation. It supports role-based Group Policy targeting, delegated administration, and separation of identity objects, but this deployment-focused repository does not claim retained screenshots of the hierarchy being populated.

```
anik.local
├── Domain Controllers          # Default DC OU
├── Servers                     # Member servers (file, print, app)
├── Workstations                # Domain-joined endpoints
├── Users
│   ├── IT_Staff                # Administrators and helpdesk
│   ├── Corporate               # Standard business users
│   └── Service_Accounts        # Non-interactive service identities
└── Groups
    ├── Security                # Permission-based access groups
    └── Distribution            # Email distribution groups
```

### Security Groups

The target access model uses **AGDLP** (Account → Global → Domain Local → Permission), a scalable and auditable approach for enterprise AD access control. The table below documents proposed groups rather than retained creation evidence.

| Group | Type | Scope | Purpose |
|---|---|---|---|
| IT_Admins | Security | Global | Full domain administrative access |
| Helpdesk | Security | Global | Password resets, user account management |
| HR_Users | Security | Global | HR file share and resource access |
| Finance_Users | Security | Global | Finance file share and resource access |
| Corporate_Users | Security | Global | Baseline access for all standard employees |
| SVC_Backup | Security | Global | Service account for backup operations |

### Target Group Policy Design

The following GPOs form the target-state policy design for extending this deployment lab into a managed domain environment.

| GPO | Linked To | Purpose |
|---|---|---|
| Default Domain Policy | anik.local | Password policy: 12-char minimum, complexity required, 90-day expiry |
| Workstation Baseline | Workstations OU | Disable USB storage, screensaver lock (10 min), restrict Control Panel |
| IT Staff Policy | Users\IT_Staff OU | Remote Desktop enabled, PowerShell execution set to RemoteSigned |
| Server Baseline | Servers OU | Windows Firewall enforced, audit policy enabled, NTP sync configured |

This repository does not claim retained `gpresult` validation for the `anik.local` build. GPO application evidence belongs to the separate `lab.local` operations repository linked above.

---

## Skills Demonstrated

`Active Directory Domain Services` · `AD Forest Deployment` · `Domain Controller Promotion` · `Organizational Unit Design` · `Security Group Design` · `AGDLP Design` · `Group Policy Design` · `DNS Integration` · `Windows Server 2022` · `Server Manager` · `PowerShell AD Deployment` · `Virtualization (UTM/QEMU)`

---

## Deployment Evidence

Screenshots from the lab environment documenting each stage of the deployment in sequence.

### 01 — Windows Server 2022 Edition Selection
![Windows Server 2022 Datacenter Evaluation selected](screenshots/01-windows-server-2022-edition-selection.png)

### 02 — OS Installation in Progress
![Windows Server installation copying files](screenshots/02-windows-server-installation-progress.png)

### 03 — Administrator Password Configuration
![Built-in Administrator password set during first-time setup](screenshots/03-administrator-password-setup.png)

### 04 — Server Manager Loading on First Boot
![Server Manager loading after first login](screenshots/04-server-manager-loading-post-boot.png)

### 05 — Server Manager Local Server Properties
![Local Server tab confirming server state before role installation](screenshots/05-server-manager-local-server-properties.png)

### 06 — Server Manager Dashboard
![Server Manager dashboard — server ready for role deployment](screenshots/06-server-manager-dashboard.png)

### 07 — Add Roles and Features — Server Selection
![Destination server selected from server pool](screenshots/07-add-roles-features-server-selection.png)

### 08 — Server Roles — AD DS Selected
![Active Directory Domain Services role checked in the roles list](screenshots/08-server-roles-adds-selected.png)

### 09 — AD DS Role Information Page
![AD DS information page — DNS requirements and DC recommendations reviewed](screenshots/09-adds-role-information-page.png)

### 10 — Installation Confirmation
![Confirmation screen: AD DS, Group Policy Management, RSAT tools all selected](screenshots/10-adds-installation-confirmation.png)

### 11 — AD DS Installation in Progress
![Feature installation running — AD DS and supporting tools installing](screenshots/11-adds-installation-progress.png)

### 12 — DC Promotion — Deployment Configuration
![Configuration Wizard: Add a new forest, anik.local](screenshots/12-adds-deployment-configuration.png)

### 13 — DC Promotion — Domain Controller Options
![DNS, Global Catalog enabled; functional level set; DSRM password configured](screenshots/13-dc-options-dns-gc-dsrm.png)

### 14 — DC Promotion — DNS Options
![No DNS delegation — correct for a standalone lab forest](screenshots/14-dns-options-no-delegation.png)

### 15 — DC Promotion — Additional Options
![NetBIOS domain name automatically assigned as ANIK](screenshots/15-additional-options-netbios-name.png)

### 16 — DC Promotion — AD DS Paths
![NTDS database, logs, and SYSVOL at default Windows paths](screenshots/16-adds-paths-ntds-sysvol.png)

### 17 — DC Promotion — PowerShell Deployment Script
![Auto-generated Install-ADDSForest script — full forest config captured](screenshots/17-adds-powershell-deployment-script.png)

### 18 — Prerequisites Check Passed
![All checks passed — DNS delegation warning noted and understood](screenshots/18-adds-prerequisites-check-passed.png)

### 19 — First Domain Logon
![ANIK\Administrator first domain authentication after promotion](screenshots/19-first-logon-domain-controller.png)

---

## Lessons Learned

- DNS has to be right before the first client touches the domain. Authentication, replication, and service discovery all depend on it, making DNS configuration a common source of AD deployment failures.
- OU design decisions made at the start are hard to undo cleanly once users and computers are in the directory. Planning the hierarchy before population matters.
- The AGDLP nesting model keeps access control scalable — assigning permissions directly to user accounts works fine at 20 users and becomes unmanageable at 200.
- The `Install-ADDSForest` script the wizard generates is genuinely useful — it documents exactly what was configured and gives you a repeatable baseline for future deployments.
- Prerequisites checks exist for a reason. The DNS delegation warning is expected in a standalone lab — but understanding *why* it appears, rather than clicking past it, is the difference between knowing AD and just following steps.

---

## Related Project

This repository focuses on the `anik.local` deployment process. For a separate `lab.local` administration environment with DHCP scopes, populated OUs, GPO enforcement on a domain-joined workstation, and layered file permissions, see the companion lab:

**[Windows-Server-2022-Enterprise-Domain](https://github.com/rahatislamanik-spec/Windows-Server-2022-Enterprise-Domain)**
158 screenshots · 2 servers · 1 domain-joined workstation · multi-site DHCP · end-to-end GPO verification

---

## Author

**Md Rahat Islam Anik**
Cloud & Infrastructure Operations Specialist · Toronto, Canada

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat&logo=linkedin)](https://linkedin.com/in/rahatislamanik)
[![GitHub](https://img.shields.io/badge/GitHub-Portfolio-181717?style=flat&logo=github)](https://github.com/rahatislamanik-spec)
