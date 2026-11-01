# Flux Comics - Active Directory and Windows Infrastructure Lab

A hands-on Windows infrastructure lab focused on **Active Directory administration, identity and access management, Group Policy, and secure file sharing**.

Flux Comics is a fictional comic publishing and retail company used to document a simulated Windows environment. The project demonstrates how directory services, security groups, file services, and endpoint policies work together in a small lab.

> **Project status:** Active development. The core directory structure, user accounts, DoomVault file share, NTFS access model, and initial endpoint controls are implemented. Access validation, automation, and additional policy work are in progress.

## Documentation

- [Infrastructure](pages/infrastructure.html) - Virtualization, server roles, network, and lab constraints
- [Active Directory](pages/active-directory.html) - Domain structure, OUs, and directory organization
- [Identity and Access](pages/identity-access.html) - User scope, security group structure, and access mapping
- [File Server](pages/file-server.html) - DoomVault, SMB, NTFS, and Access-Based Enumeration
- [Group Policy](pages/group-policy.html) - Endpoint policy scope and implementation status

## Lab Environment

| Component | Configuration |
|---|---|
| Organization | Flux Comics |
| Hypervisor | VMware Workstation Pro |
| Server | Windows Server 2025 virtual machine |
| Clients | Two Windows 11 virtual machines |
| Active Directory domain | `ad.comics.internal` |
| Network | Custom VMnet with server IP reserved through DHCP |
| Employee accounts | 10 fictional employees across four departments |

The domain controller and file server roles run on the same Windows Server 2025 virtual machine because of local hardware limits. This is a lab constraint, not a production design.

## Architecture Overview

The lab separates directory objects into users, workstations, servers, distribution groups, and security groups.

```text
Root
├── Latveria
│   ├── Distribution Groups
│   ├── Security Groups
│   ├── Servers
│   └── Users
│       └── Doomstadt
│           ├── IT & Administration
│           ├── Management
│           ├── Publishing & Creative
│           └── Retail Operations
└── Workstations
    └── Doomstadt
        └── Retail Operations
```

The environment uses AD DS and DNS for directory and name services. Group Policy provides centralized endpoint controls, while DoomVault provides SMB-based file storage.

## Identity and Access

The lab contains ten fictional employee accounts across four departments:

| Department | Employees |
|---|---:|
| Management | 2 |
| Retail Operations | 3 |
| Publishing & Creative | 3 |
| IT & Administration | 2 |
| **Total** | **10** |

Security groups follow the general naming pattern:

```text
<Type>_<Resource>_<Role>
```

The supported group type prefixes are **G** for Global, **DL** for Domain Local, and **U** for Universal. The SMB folder implementation currently uses Global groups such as:

- `G_Company_Read`
- `G_Company_Modify`

Equivalent read and modify groups exist for the other DoomVault root folders. Group membership completion and effective access validation are still in progress.

All employee identities and organizational details are fictional and created for this lab.

## DoomVault File Server

DoomVault is the lab's SMB file share. It is stored on a dedicated virtual disk separate from the standard system disk.

| Property | Configuration |
|---|---|
| Share name | `DoomVault` |
| Local path | `E:\shares` |
| Network path | `\\<FileServerName>\DoomVault` |
| Storage | Dedicated virtual disk `E:` |
| SMB encryption | Enabled in transit |
| Access-Based Enumeration | Enabled |
| Folder-level control | NTFS access control entries |

```text
E:\shares\
├── Company
├── IT Software
├── Management
├── Publishing Creative
└── Retail Operations
```

Users connect through SMB. The share permission includes `Everyone`, while NTFS permissions and security group membership enforce effective access to folders and files. Share permissions and NTFS permissions work together; the `Everyone` share permission does not replace the NTFS controls. Access-Based Enumeration hides folders users cannot access, reducing visible attack surface and navigation noise.

## Group Policy

Group Policy is being used to apply centralized controls to users and workstations. The current policy work focuses on endpoint security, workstation scope, session behavior, privilege handling, and centralized logon messaging.

Detailed GPO configuration, scope, validation, and future policy work are documented on the [Group Policy page](pages/group-policy.html).

## Current Status

### Implemented

- AD DS domain structure and OU hierarchy
- Ten fictional employee accounts across four departments
- DoomVault SMB share and folder structure
- NTFS-based access model with security groups
- SMB encryption in transit and Access-Based Enumeration
- Initial Group Policy endpoint controls

### In Progress

- Security group membership completion
- Effective access validation across folder permissions
- Endpoint behavior validation
- Additional Group Policy development

### Planned

- Account provisioning and group assignment automation
- Expanded endpoint hardening controls
- Additional troubleshooting and administration documentation

## Skills Demonstrated

- Windows Server administration
- Active Directory Domain Services
- Active Directory users, groups, and Organizational Units
- Security group and role-based access management
- SMB network shares
- NTFS permissions and access control
- Access-Based Enumeration
- SMB encryption in transit
- Group Policy administration
- Windows endpoint hardening
- Infrastructure documentation and administration planning

## Lab Disclaimer

Flux Comics is fictional. All employees, departments, job titles, directory objects, and file contents are simulated for educational and portfolio demonstration only. The `ad.comics.internal` domain exists only inside the isolated lab.
