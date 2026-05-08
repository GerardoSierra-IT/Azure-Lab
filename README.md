# Enterprise Hybrid Identity & Zero Trust Lab in Azure
# Enterprise Hybrid Identity & Zero Trust Lab in Azure

## Project Overview

This project demonstrates the design and deployment of a cost-optimized enterprise hybrid identity environment in Microsoft Azure using Active Directory Domain Services (AD DS), Microsoft Entra ID (Azure AD), Conditional Access, PowerShell automation, and Microsoft Sentinel.

The goal of this lab was to simulate a real-world enterprise identity architecture while maintaining a minimal operational cost using a single Azure virtual machine.

This project focuses heavily on:

* Hybrid Identity
* Zero Trust Security Principles
* Active Directory Administration
* Azure Identity Integration
* Conditional Access
* SIEM Monitoring & Detection
* PowerShell Automation
* Cloud Troubleshooting

---

# Skills Demonstrated

## Identity & Access Management

* Active Directory Domain Services (AD DS)
* Microsoft Entra ID (Azure AD)
* Hybrid Identity Synchronization
* Organizational Unit (OU) Design
* Role-Based Access Control (RBAC)
* Group Policy Management (GPO)

## Cloud & Infrastructure

* Azure Virtual Machines
* Azure Networking
* Network Security Groups (NSGs)
* Azure Resource Management
* Cost Optimization

## Security Engineering

* Zero Trust Architecture
* Conditional Access Policies
* Multi-Factor Authentication (MFA)
* Account Lockout Policies
* Security Hardening
* Audit Logging

## SIEM & Monitoring

* Microsoft Sentinel
* Log Analytics
* Authentication Monitoring
* Failed Login Detection

## Automation

* PowerShell User Provisioning
* Group Membership Automation
* Identity Management Automation

---

# Architecture Overview

## Environment Design

This lab uses a single Azure VM architecture to minimize costs while still demonstrating enterprise identity concepts.

### Components

| Component          | Purpose                                |
| ------------------ | -------------------------------------- |
| DC01               | Domain Controller + Entra Connect Sync |
| Microsoft Entra ID | Cloud Identity Platform                |
| Microsoft Sentinel | SIEM Monitoring                        |
| Azure NSG          | Restrict RDP Access                    |
| PowerShell         | Automation                             |

---

# Cost Optimization Strategy

This lab was intentionally designed to stay under approximately $5.

## Cost Controls Implemented

* B1s VM Size
* Standard HDD/SSD storage
* Auto-shutdown enabled
* Single-VM architecture
* Minimal Sentinel ingestion
* Resource cleanup after completion

Estimated total cost:

* ~$0.30 - $1.00 for several hours of use

---

# PHASE 1 — Azure Environment Deployment

## Step 1 — Create Resource Group

1. Log into Azure Portal
2. Search for:

   * Resource Groups
3. Click:

   * Create

### Configuration

| Setting             | Value             |
| ------------------- | ----------------- |
| Resource Group Name | rg-zero-trust-lab |
| Region              | East US           |

4. Click:

   * Review + Create
   * Create

---

## Step 2 — Create Virtual Network

1. Search:

   * Virtual Networks
2. Click:

   * Create

### Configuration

| Setting       | Value       |
| ------------- | ----------- |
| Name          | vnet-lab    |
| Address Space | 10.0.0.0/24 |
| Subnet Name   | subnet-lab  |
| Subnet Range  | 10.0.0.0/25 |

3. Click:

   * Review + Create
   * Create

---

## Step 3 — Create Azure Virtual Machine

1. Search:

   * Virtual Machines
2. Click:

   * Create
   * Azure Virtual Machine

### Basics Tab

| Setting  | Value                          |
| -------- | ------------------------------ |
| Name     | DC01                           |
| Region   | East US                        |
| Image    | Windows Server 2022 Datacenter |
| Size     | B1s                            |
| Username | labadmin                       |

### Disk Configuration

| Setting   | Value        |
| --------- | ------------ |
| Disk Type | Standard SSD |

### Networking

| Setting         | Value      |
| --------------- | ---------- |
| Virtual Network | vnet-lab   |
| Subnet          | subnet-lab |
| Public IP       | Enabled    |

3. Click:

   * Review + Create
   * Create

---

## Step 4 — Configure Network Security Group (NSG)

The VM was secured by restricting RDP access to only the administrator's public IP.

### Steps

1. Open VM
2. Go to:

   * Networking
3. Create inbound rule

### Rule Configuration

| Setting | Value |
| ------- | ----- |
| Source  | My IP |
| Port    | 3389  |
| Action  | Allow |

This prevents unrestricted internet access to the VM.

---

# PHASE 2 — Active Directory Deployment

## Step 1 — Connect to VM via RDP

1. Copy VM Public IP
2. Open Remote Desktop Connection
3. Connect using:

| Setting  | Value                      |
| -------- | -------------------------- |
| Username | labadmin                   |
| Password | Configured during VM setup |

---

## Step 2 — Install Active Directory Domain Services

1. Open:

   * Server Manager

2. Click:

   * Manage
   * Add Roles and Features

3. Continue until:

   * Server Roles

4. Select:

   * Active Directory Domain Services

5. Click:

   * Add Features
   * Install

---

## Step 3 — Promote Server to Domain Controller

1. Open notifications flag
2. Click:

   * Promote this server to a domain controller

### Configuration

| Setting     | Value          |
| ----------- | -------------- |
| Deployment  | Add new forest |
| Root Domain | corp.local     |

3. Configure DSRM password
4. Continue through installation
5. Reboot VM

---

# PHASE 3 — Organizational Unit & User Design

## Step 1 — Open Active Directory Users and Computers

1. Open:

   * Server Manager
2. Click:

   * Tools
   * Active Directory Users and Computers

---

## Step 2 — Create Organizational Units

Created the following OUs:

* Tier0_Admins
* HR
* Workstations
* Servers

These OUs simulate enterprise administrative segmentation.

---

## Step 3 — Create Users

### Example Accounts

| User     | Purpose              |
| -------- | -------------------- |
| admin.t0 | Tier 0 Administrator |
| user.hr  | Standard HR User     |

---

# PHASE 4 — Microsoft Entra ID Hybrid Identity Integration

## Objective

This phase integrates on-premises Active Directory with Microsoft Entra ID to create a hybrid identity environment.

---

## Step 1 — Open Microsoft Entra Admin Center

1. Navigate to:

   * [https://entra.microsoft.com](https://entra.microsoft.com)

2. Sign in using Azure account

---

## Step 2 — Download Microsoft Entra Connect Sync

1. Inside Entra Admin Center

2. Navigate to:

   * Identity
   * Microsoft Entra Connect

3. Open:

   * Connect Sync

4. Click:

   * Download

---

## Step 3 — Install Entra Connect Sync

1. Transfer installer to VM if needed
2. Run installer
3. Select:

   * Express Settings

---

## Step 4 — Connect Azure Tenant

1. Sign into Microsoft Entra ID
2. Authenticate using Azure account

---

## Step 5 — Connect On-Premises Active Directory

### Configuration

| Setting  | Value         |
| -------- | ------------- |
| Domain   | corp.local    |
| Username | corp\labadmin |

---

## Step 6 — Configure Synchronization

### Selected Option

* Password Hash Synchronization

This synchronizes on-premises user identities to the cloud.

---

## Step 7 — Verify Synchronization

1. Return to:

   * Microsoft Entra Admin Center

2. Navigate to:

   * Users

3. Verify synchronized accounts appear

Example:

* user.hr

---

# PHASE 5 — Zero Trust Security Controls

## Objective

Implement Zero Trust security principles using Conditional Access and MFA.

---

## Step 1 — Open Conditional Access

1. Open Microsoft Entra Admin Center
2. Navigate to:

   * Protection
   * Conditional Access

---

## Step 2 — Create MFA Policy

### Policy Configuration

| Setting       | Value       |
| ------------- | ----------- |
| Policy Name   | Require MFA |
| Users         | All Users   |
| Grant Control | Require MFA |

This enforces stronger authentication security.

---

## Step 3 — Configure Additional Security Policies

Additional policies implemented:

* Block legacy authentication
* Require MFA for administrators
* Restrict insecure authentication methods

---

# PHASE 6 — Group Policy Hardening

## Security GPOs Implemented

### Account Lockout Policy

Configured account lockout after multiple failed login attempts.

---

### Audit Logging Policy

Enabled auditing for:

* Logon events
* Failed logins
* Privilege use
* Account lockouts

---

### PowerShell Restrictions

Configured PowerShell execution restrictions to reduce attack surface.

---

# PHASE 7 — Attack Simulation & Detection

## Objective

Simulate authentication attacks and monitor detection capabilities.

---

## Step 1 — Simulate Failed Logins

From a separate device:

1. Attempt multiple failed sign-ins
2. Trigger account lockout and audit logs

---

# PHASE 8 — Microsoft Sentinel SIEM Integration

## Step 1 — Create Log Analytics Workspace

1. Search:

   * Log Analytics Workspaces
2. Create workspace

### Configuration

| Setting | Value   |
| ------- | ------- |
| Name    | law-lab |
| Region  | East US |

---

## Step 2 — Enable Microsoft Sentinel

1. Search:

   * Microsoft Sentinel
2. Click:

   * Create
3. Select Log Analytics Workspace

---

## Step 3 — Configure Data Connectors

Enabled connectors:

* Microsoft Entra ID Logs
* Security Events

---

## Step 4 — Create Analytics Rule

Configured alerting for:

* Multiple failed login attempts
* Suspicious authentication activity

---

# PHASE 9 — PowerShell Automation

## Objective

Automate user provisioning and group membership.

---

## Create User via PowerShell

```powershell
New-ADUser -Name "Test User" `
-SamAccountName test.user `
-AccountPassword (ConvertTo-SecureString "P@ss1234" -AsPlainText -Force) `
-Enabled $true
```

---

## Add User to Group

```powershell
Add-ADGroupMember -Identity "HR" -Members "test.user"
```

---

# Troubleshooting & Challenges Encountered

## Azure Subscription Restrictions

Initially discovered that the original Azure subscription was restricted, preventing access to required VM SKUs.

Resolution:

* Upgraded to Pay-As-You-Go subscription

---

## Azure Policy Misconfiguration

A custom Azure Policy restricting VM SKUs caused deployment limitations even after deletion.

Resolution:

* Removed policy assignments
* Verified deletion through activity logs
* Re-tested deployment using clean resource groups

---

## Resource Recreation

Rebuilt networking resources multiple times to eliminate possible NSG, VNet, or resource group misconfiguration.

Resources recreated:

* Virtual Networks
* NSGs
* Resource Groups

---

## Compute Quota Limitation

Discovered B-series VM deployment was blocked because the subscription quota for B-series vCPUs was set to zero.

Resolution:

* Submitted quota increase request
* Successfully enabled B-series VM deployment

---

# Security Concepts Demonstrated

* Zero Trust Security
* Least Privilege Access
* Identity Synchronization
* Hybrid Identity Architecture
* Conditional Access
* Security Monitoring
* Attack Detection
* Enterprise Hardening

---

# Key Takeaways

This project provided hands-on experience troubleshooting Azure infrastructure, identity synchronization, policy enforcement, and quota limitations while implementing enterprise security controls in a cost-efficient cloud environment.

The lab reinforced the importance of:

* Methodical troubleshooting
* Cloud identity security
* Infrastructure hardening
* Cost optimization
* Automation
* SIEM visibility

---

# Future Improvements

Potential future enhancements:

* Multi-domain architecture
* Azure Bastion deployment
* Intune device compliance integration
* Privileged Identity Management (PIM)
* Defender for Cloud integration
* Automated incident response playbooks

---

# Author

This project was designed and deployed as part of a self-directed cloud security and hybrid identity engineering lab focused on enterprise infrastructure, identity management, and Zero Trust architecture.
