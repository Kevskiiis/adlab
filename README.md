# Flux Comics — Active Directory & Windows Infrastructure Lab

A hands-on Windows infrastructure lab focused on **Active Directory administration, identity and access management, Group Policy, and secure file sharing**.

The lab simulates the IT environment of a fictional comic publishing and retail company, Flux Comics. It is designed to demonstrate practical Windows administration skills through a structured, security-focused environment.

> **Project status:** Active development. Active Directory, user accounts, organizational units, the DoomVault network share, NTFS access controls, and initial Group Policy controls have been implemented. Additional security policies and automation are in progress.

---

## Table of Contents

- [Project Overview](#project-overview)
- [Lab Environment](#lab-environment)
- [Active Directory Structure](#active-directory-structure)
- [Identity and Access Management](#identity-and-access-management)
- [DoomVault File Server](#doomvault-file-server)
- [Group Policy and Endpoint Security](#group-policy-and-endpoint-security)
- [Planned Improvements](#planned-improvements)
- [Skills Demonstrated](#skills-demonstrated)

---

## Project Overview

Flux Comics is a fictional organization with employees across Management, Retail Operations, Publishing & Creative, and IT & Administration.

The lab models common infrastructure responsibilities, including:

- Centralized user and computer management with Active Directory.
- Organizational Units (OUs) for structured directory administration.
- Global security groups for access management.
- NTFS permissions and SMB file sharing.
- Group Policy-based endpoint hardening.
- Department-specific security controls.
- Future automation of account provisioning and group membership.

The goal is to build a practical environment for learning how Windows infrastructure components work together, while documenting the architecture and security decisions behind the implementation.

## Lab Environment

| Component | Configuration |
|---|---|
| Organization | Flux Comics |
| Active Directory domain | `ad.comics.internal` |
| Office location | Doomstadt |
| Domain controller | Windows Server with AD DS and DNS |
| File server | Windows Server |
| Network share | `DoomVault` |
| Client systems | Two Windows client machines |
| Employee accounts | 10 fictional users |

Due to local hardware constraints, the domain controller and file server roles are hosted on the same Windows Server instance. This is a **lab-specific constraint**, not a recommended production architecture.

## Active Directory Structure

The directory is organized to separate users, workstations, servers, and security groups.

    Root
    ├── Latveria
    │   ├── Distribution Groups
    │   ├── Security Groups
    │   │   ├── Everyone
    │   │   ├── IT & Administration
    │   │   ├── Management
    │   │   ├── Network Shares
    │   │   ├── Publishing & Creative
    │   │   └── Retail Operations
    │   ├── Servers
    │   └── Users
    │       └── Doomstadt
    │           ├── IT & Administration
    │           ├── Management
    │           ├── Publishing & Creative
    │           └── Retail Operations
    │
    └── Workstations
        └── Doomstadt
            └── Retail Operations

The OU structure provides a foundation for organizing directory objects and applying Group Policy based on administrative scope and workstation role.

## Identity and Access Management

The lab includes 10 fictional employee accounts across four departments.

| Department | Employees |
|---|---:|
| Management | 2 |
| Retail Operations | 3 |
| Publishing & Creative | 3 |
| IT & Administration | 2 |
| **Total** | **10** |

### Employee Directory

| Name | Department | Job Title |
|---|---|---|
| Marcus Bennett | Management | Company Owner |
| Olivia Chen | Management | Operations Manager |
| Ethan Brooks | Retail Operations | Retail Supervisor |
| Sophia Patel | Retail Operations | Sales Associate |
| Noah Williams | Retail Operations | Inventory Specialist |
| Ava Mitchell | Publishing & Creative | Publishing Coordinator |
| Lucas Rivera | Publishing & Creative | Comic Artist |
| Isabella Foster | Publishing & Creative | Editor |
| Daniel Murphy | IT & Administration | IT Support Technician |
| Grace Thompson | IT & Administration | IT Administrator |

All employee identities are fictional and created for this lab.

### Security Group Strategy

Global security groups are used to organize access to shared resources.

The lab uses a role-based naming convention that describes the resource and access level:

- `G_<Resource>_Read`
- `G_<Resource>_Modify`

For example:

| Group | Intended Access |
|---|---|
| `G_Company_Read` | Read access to the Company folder |
| `G_Company_Modify` | Modify access to the Company folder |

Equivalent read and modify groups have been created for the other root folders in DoomVault.

This design separates **user identity from resource permissions**. Instead of assigning permissions directly to individual users, access is intended to be managed through security-group membership.

User-to-group membership management and provisioning automation are still in progress.

## DoomVault File Server

The lab includes an SMB network share named **DoomVault**, hosted on the file server.

| Property | Configuration |
|---|---|
| Local path | `E:\shares` |
| SMB share name | `DoomVault` |
| Network path | `\\<FileServerName>\DoomVault` |
| SMB encryption in transit | Enabled |
| Access-Based Enumeration | Enabled |
| Folder-level access control | NTFS permissions |

### Folder Structure

    E:\shares\
    ├── Company
    ├── IT Software
    ├── Management
    ├── Publishing Creative
    └── Retail Operations

The share provides centralized storage for company-wide and department-specific resources.

### Access Control Model

The SMB share currently has an `Everyone` share permission, while access is hardened at the folder level through NTFS permissions and security groups.

The intended access model is:

1. Users connect to DoomVault through SMB.
2. NTFS permissions determine access to individual folders and files.
3. Read and modify security groups provide access based on assigned responsibilities.
4. Access-Based Enumeration helps hide folders that users do not have permission to access.

Share permissions and NTFS permissions work together to constrain network access. The `Everyone` share permission does not replace the NTFS access controls.

## Group Policy and Endpoint Security

Group Policy is being used to apply centralized security settings to users and workstations.

The following policies have been implemented:

| Group Policy | Scope | Purpose |
|---|---|---|
| Block All Removable Storage Devices | Retail Operations workstations | Restricts removable-storage device use |
| Block Firewall Inbound | Retail Operations workstations | Blocks inbound firewall traffic according to the configured policy |
| Logon Inactivity | All systems | Applies a 180-second inactivity limit |
| Auto Deny Elevated Privilege | Retail Operations | Denies elevation requests for standard users rather than prompting for credentials |
| Company Logon Message | Company systems | Displays a company logon title and message |

These controls provide a starting point for centralized endpoint hardening and department-specific security enforcement.

Additional user and computer policies are being developed to expand security coverage across the lab.

## Planned Improvements

The lab is actively evolving. Planned work includes:

- Expanding Group Policy security controls for users and computers.
- Completing and validating user-to-security-group membership.
- Automating user account provisioning.
- Automating department-based group membership.
- Testing access to DoomVault with accounts from different departments.
- Documenting permission troubleshooting and validation.
- Continuing to refine the security baseline for workstations.

## Skills Demonstrated

This project provides hands-on experience with:

- Windows Server administration
- Active Directory Domain Services
- Active Directory Users, Groups, and Organizational Units
- Global security groups and role-based access management
- SMB network shares
- NTFS permissions and access control
- Access-Based Enumeration
- SMB encryption in transit
- Group Policy administration
- Windows endpoint hardening
- Department-specific security policies
- Infrastructure documentation
- Account provisioning and administration planning

---

## Project Outcome

The Flux Comics lab demonstrates the development of a small-business Windows infrastructure environment, with centralized identity management, structured directory organization, controlled file sharing, and initial endpoint security policies.

The project is being expanded with additional Group Policy controls, access validation, and automation to improve consistency and reduce manual administration.
