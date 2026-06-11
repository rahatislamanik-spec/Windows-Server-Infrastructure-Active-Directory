# Windows Server Infrastructure & Active Directory Administration

### Active Directory · DNS · DHCP · Group Policy · Windows Server 2022

**Md Rahat Islam Anik · George Brown College · Cloud Computing & Network Administration (T465) · Postgraduate**

[![GitHub Repo](https://img.shields.io/badge/GitHub-Repository-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/rahatislamanik-spec/Windows-Server-Infrastructure-Active-Directory)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-rahatislamanik-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/rahatislamanik)

---

## Overview

This project documents the deployment and configuration of a Windows Server 2022 Active Directory environment — from OS installation through Domain Controller promotion and first domain logon.

The environment is structured as a single-domain Active Directory forest. Every stage of the deployment was configured hands-on and captured as evidence: OS installation, Server Manager role deployment, AD DS configuration wizard, DNS integration, DC promotion, and domain authentication.

---

## Architecture Diagram

![Windows Server 2022 Domain Controller Architecture](assets/windows-server-2022-domain-controller-architecture-v2.svg)

This diagram illustrates the Windows Server 2022 Active Directory environment, including Active Directory Domain Services (AD DS), DNS, DHCP, Organizational Units (OUs), Security Groups, Group Policy Objects (GPOs), and domain-joined client systems — showing the operational flow of IP assignment, DNS resolution, domain join, authentication, and Group Policy processing.

---

## Environment Summary

| Component | Details |
|---|---|
| Domain | anik.local |
| Domain Controller | Windows Server 2022 Datacenter Evaluation |
| Virtualization | UTM (QEMU/x86_64) on macOS |
| Services Deployed | AD DS, DNS |
| Management Tools | Server Manager, ADUC, GPMC, DNS Manager |

---

## What Was Built

### Windows Server 2022 — OS Installation & Initial Configuration

Windows Server 2022 Datacenter Evaluation (Desktop Experience) was installed on a virtualized x86_64 environment. The VM was allocated 4 vCPUs, 8 GB RAM, and 64 GB storage. Initial configuration included setting the built-in Administrator password and verifying Server Manager loaded on first login.

### Active Directory Domain Services — Role Deployment

The AD DS role was installed via the Add Roles and Features Wizard in Server Manager, along with Group Policy Management and Remote Server Administration Tools. Installation was confirmed before proceeding to domain controller promotion.

### Domain Controller Promotion — Forest Deployment

The server was promoted to Domain Controller using the AD DS Configuration Wizard. A new Active Directory forest was created with the root domain `anik.local`. Configuration included:

- DNS Server and Global Catalog enabled at promotion
- DSRM password configured
- DNS delegation warning acknowledged (expected in standalone lab)
- NetBIOS name automatically assigned as `ANIK`
- Default paths confirmed: `C:\Windows\NTDS` (database and logs), `C:\Windows\SYSVOL`
- All prerequisites passed successfully prior to installation

The wizard generated a PowerShell deployment script (`Install-ADDSForest`) documenting the full configuration — suitable for repeatable or automated deployments.

### DNS Integration

DNS was integrated at promotion, establishing `anik.local` as the authoritative zone. Without correctly configured DNS, Active Directory authentication, service discovery, and replication cannot function — DNS is the foundational dependency of every AD environment.

### Domain Authentication — First Logon

Following promotion and automatic reboot, the server authenticated successfully as `ANIK\Administrator`, confirming the domain is operational and the DC is functioning correctly.

---

## Tech Stack

| Category | Tools & Services |
|---|---|
| Server OS | Windows Server 2022 Datacenter Evaluation |
| Virtualization | UTM (QEMU x86_64) |
| Directory Services | Active Directory Domain Services (AD DS) |
| Policy Management | Group Policy Management Console (GPMC) |
| Name Resolution | DNS Server (integrated at DC promotion) |
| Administration | Server Manager, ADUC |

---

## Skills Demonstrated

`Active Directory Domain Services` · `AD Forest Deployment` · `Domain Controller Promotion` · `DNS Integration` · `Group Policy Management` · `Windows Server 2022` · `Server Manager` · `PowerShell AD Deployment` · `Virtualization (UTM/QEMU)`

---

## Deployment Evidence

Real screenshots from the lab environment documenting each stage of the deployment.

### 01 — Windows Server 2022 Edition Selection
![Windows Server 2022 Datacenter Evaluation selected](screenshots/01-windows-server-2022-edition-selection.png)

### 02 — OS Installation in Progress
![Windows Server installation copying files](screenshots/02-windows-server-installation-progress.png)

### 03 — Administrator Password Configuration
![Built-in Administrator password set during first-time setup](screenshots/03-administrator-password-setup.png)

### 04 — Server Manager Loading on First Boot
![Server Manager loading automatically after first login](screenshots/04-server-manager-loading-post-boot.png)

### 05 — Server Manager Local Server Properties
![Server Manager Local Server tab showing server properties](screenshots/05-server-manager-local-server-properties.png)

### 06 — Server Manager Dashboard
![Server Manager dashboard confirming server is ready for role installation](screenshots/06-server-manager-dashboard.png)

### 07 — Add Roles and Features — Server Selection
![Add Roles and Features Wizard: destination server selected from server pool](screenshots/07-add-roles-features-server-selection.png)

### 08 — Server Roles — AD DS Selected
![Active Directory Domain Services role selected in Server Roles list](screenshots/08-server-roles-adds-selected.png)

### 09 — AD DS Role Information Page
![AD DS information page outlining DNS requirements and DC recommendations](screenshots/09-adds-role-information-page.png)

### 10 — AD DS Installation Confirmation
![Confirmation screen showing AD DS, Group Policy Management, and RSAT tools selected](screenshots/10-adds-installation-confirmation.png)

### 11 — AD DS Installation in Progress
![Feature installation progress bar — AD DS and supporting tools installing](screenshots/11-adds-installation-progress.png)

### 12 — DC Promotion — Deployment Configuration
![AD DS Configuration Wizard: Add a new forest, anik.local domain specified](screenshots/12-adds-deployment-configuration.png)

### 13 — DC Promotion — Domain Controller Options
![DC Options: DNS, Global Catalog enabled; forest and domain functional level Windows Server 2016; DSRM password configured](screenshots/13-dc-options-dns-gc-dsrm.png)

### 14 — DC Promotion — DNS Options
![DNS Options: no delegation required; warning acknowledged for standalone lab](screenshots/14-dns-options-no-delegation.png)

### 15 — DC Promotion — Additional Options (NetBIOS)
![Additional Options: NetBIOS domain name automatically set to ANIK](screenshots/15-additional-options-netbios-name.png)

### 16 — DC Promotion — AD DS Paths
![Paths page: NTDS database, log files, and SYSVOL at default Windows paths](screenshots/16-adds-paths-ntds-sysvol.png)

### 17 — DC Promotion — PowerShell Deployment Script
![Auto-generated PowerShell Install-ADDSForest script documenting the full forest configuration](screenshots/17-adds-powershell-deployment-script.png)

### 18 — DC Promotion — Prerequisites Check Passed
![All prerequisite checks passed; DNS delegation warning noted; ready to install](screenshots/18-adds-prerequisites-check-passed.png)

### 19 — First Domain Logon — ANIK\Administrator
![Server rebooted post-promotion; first logon as ANIK\Administrator confirming domain is operational](screenshots/19-first-logon-domain-controller.png)

---

## Lessons Learned

- DNS is the foundational dependency of Active Directory — authentication, service discovery, and replication all depend on it being correctly configured at promotion.
- The AD DS Configuration Wizard auto-generates a PowerShell deployment script, enabling repeatable and auditable forest deployments.
- Prerequisites validation is a critical gate before DC promotion — warnings must be understood, not bypassed.
- Functional level selection at promotion determines which AD features are available across the forest and domain.

---

## Author

**Md Rahat Islam Anik**
Cloud Computing & Network Administration · George Brown College · May 2026

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat&logo=linkedin)](https://linkedin.com/in/rahatislamanik)
[![GitHub](https://img.shields.io/badge/GitHub-Portfolio-181717?style=flat&logo=github)](https://github.com/rahatislamanik-spec)
