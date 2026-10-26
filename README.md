# Small Business IT Infrastructure & Active Directory Lab

## 1. Project Overview

### Project Summary

This project involves designing, implementing, securing, and maintaining a centralized Windows IT environment for a fictional small business with 20 employees.

The lab simulates the responsibilities of a systems administrator who manages the company's IT infrastructure, including user accounts, Windows workstations, file storage, access permissions, security policies, and routine administrative operations.

The environment is built around Microsoft Active Directory Domain Services (AD DS), providing centralized identity management and a foundation for consistent security and workstation administration.

The project emphasizes practical systems administration rather than simply installing and configuring individual services. Each component is implemented to address a specific business requirement, and its functionality is validated through hands-on testing.

The lab uses an existing Active Directory domain:

`ad.comics.internal`

The domain will support the fictional company's identity and infrastructure management requirements.

### Project Objectives

- Manage a centralized Active Directory domain.
- Design an appropriate Organizational Unit (OU) structure.
- Establish consistent user and computer account management procedures.
- Implement security groups and role-based access controls.
- Configure a Windows file server with departmental shared folders.
- Enforce workstation security settings through Group Policy.
- Implement delegated administration and least-privilege access.
- Troubleshoot common DNS, authentication, Group Policy, and file access issues.
- Automate repetitive administrative tasks using PowerShell.
- Configure and validate appropriate Windows security auditing.
- Develop backup, recovery, and operational documentation.
- Document the environment in a way that another administrator can understand and maintain.

**Target roles:** Junior Systems Administrator, IT Administrator, Windows Systems Administrator, IT Support Technician.

**Environment:** Virtualized Windows Server and Windows client machines.

**Primary technologies:** Active Directory Domain Services, DNS, Group Policy, Windows Server, SMB file sharing, NTFS permissions, PowerShell, Windows Event Viewer, and Remote Server Administration Tools (RSAT).

---

## 2. Business Scenario: Panel & Pixel Comics

### Company Background

Panel & Pixel Comics is a fictional small business specializing in comic book retail, independent comic publishing, and original merchandise.

The company has recently opened its first office and retail location. Its 20 employees work across several business functions, including management, retail operations, publishing, and IT support.

As the business grows, management wants to establish a centralized IT environment that supports employee productivity, protects company information, and reduces the amount of manual administration required.

The company has hired a systems administrator to establish and maintain its internal Windows infrastructure.

You are responsible for designing, implementing, documenting, and supporting this environment.

### Company Profile

| Attribute | Description |
|---|---|
| Company | Panel & Pixel Comics |
| Industry | Comic book retail and publishing |
| Employees | 20 |
| Locations | One primary office and retail location |
| IT staffing | One systems administrator |
| Primary client systems | Windows workstations |
| Identity platform | Active Directory Domain Services |
| Active Directory domain | `ad.comics.internal` |
| File storage | Centralized Windows file server |
| Network environment | Private business network |
| IT operating model | Centrally managed Windows infrastructure |

### Employee Distribution

The company has four business functions.

| Department | Employees | Responsibilities |
|---|---:|---|
| Management | 2 | Business decisions, finances, and personnel oversight |
| Retail Operations | 8 | Inventory, sales, customer service, and store operations |
| Publishing & Creative | 7 | Comic production, artwork, publishing, and marketing |
| IT & Administration | 3 | Technical support, infrastructure, and administrative operations |
| **Total** | **20** | |

Although the company has 20 employees, the lab will use a smaller set of representative accounts to demonstrate the required administrative functionality.

You do not need to create all 20 employee accounts to demonstrate the design.

### Current Business Challenges

As the company's first systems administrator, you have identified several infrastructure requirements.

**1. Centralized Identity Management**

Employees need individual accounts to authenticate to company workstations and access internal resources. Accounts must be created and maintained consistently.

**2. Departmental Data Protection**

The publishing team works with original artwork, scripts, and unpublished content. Management maintains sensitive business records. These resources must not be accessible to every employee.

**3. Shared Business Resources**

Employees need centralized access to department-specific documents, templates, and other working files.

**4. Consistent Workstation Configuration**

The company needs a standard approach to configuring Windows workstations and applying appropriate security settings.

**5. Controlled Administrative Access**

The systems administrator needs sufficient privileges to manage the infrastructure. Routine support activities should not require unrestricted domain privileges.

**6. Reliable IT Operations**

The company needs documented procedures for common issues, including account lockouts, failed logins, inaccessible file shares, and Group Policy problems.

**7. Data Protection and Recovery**

Management expects important business documents to be recoverable after accidental deletion or other failures.

### Business Goal

Establish a secure, maintainable Windows domain environment that supports the company's 20 employees without introducing unnecessary complexity or administrative overhead.

The solution must be appropriate for a small business while demonstrating sound systems administration practices.

---

## 3. Project Scope

### In Scope

The project includes:

- Active Directory domain administration.
- DNS configuration and troubleshooting.
- OU and security group design.
- User account lifecycle management.
- Domain-joined workstation management.
- Windows file server configuration.
- SMB sharing and NTFS permissions.
- Group Policy configuration and troubleshooting.
- Delegated administration.
- Privileged account separation.
- PowerShell administration and automation.
- Windows security auditing.
- Backup and recovery procedures.
- Infrastructure troubleshooting.
- Operational documentation.

### Out of Scope

The initial implementation does not require:

- A geographically distributed enterprise network.
- Multiple office locations.
- Enterprise-wide endpoint detection and response.
- A production SIEM deployment.
- Publicly accessible domain controllers.
- Complex enterprise identity federation.
- High-availability clustering.
- A large fleet of physical workstations.
- A complete cloud infrastructure deployment.

These capabilities may be explored as future enhancements.

### Implementation Philosophy

The infrastructure should remain simple enough for one administrator to maintain while incorporating security and operational practices that would be appropriate in a real business.

The goal is not to recreate an enterprise environment with unnecessary complexity. The goal is to implement the core services a small business needs and demonstrate that they work correctly.

---

## 4. Infrastructure Architecture

### Proposed Environment

The lab will use a small number of virtual machines to represent the company's core IT infrastructure.

| System | Role | Responsibilities |
|---|---|---|
| `DC01` | Domain Controller | Active Directory, DNS, authentication, and Group Policy support |
| `FILE01` | File Server | Departmental file storage and access control |
| `CLIENT01` | Windows Workstation | General employee account and Group Policy testing |
| `CLIENT02` | Windows Workstation | Additional user, department, and access-control testing |
| `ADMIN01` | Optional Administration Workstation | Remote administration using RSAT |

The existing Windows Server hosting Active Directory will serve as `DC01` conceptually, even if its current hostname differs.

The proposed system names are logical labels. Rename existing machines only if there is a clear reason to do so.

`ADMIN01` is optional. The initial project can be completed without a dedicated administration workstation.

### Infrastructure Design

The intended logical architecture is:

```text
                  Panel & Pixel Comics
                           |
                           |
                 Active Directory Domain
                    ad.comics.internal
                           |
                           v
                         DC01
                  Active Directory + DNS
                           |
             +-------------+-------------+
             |             |             |
             v             v             v
          CLIENT01      CLIENT02       FILE01
          Windows       Windows        File Server
          Workstation   Workstation        |
                                           |
                                  Departmental Shares
                                           |
                         +-----------------+----------------+
                         |                 |                |
                         v                 v                v
                    Management          Retail         Publishing
                                         Ops            & Creative
```

This is a logical diagram, not a complete network topology. Network addressing, virtual switches, routing, and firewall configuration will be documented separately where relevant.

### Infrastructure Requirements

The environment must:

1. Maintain reliable domain and DNS functionality.
2. Allow domain-joined clients to locate a domain controller.
3. Support centralized user authentication.
4. Provide access to departmental file shares.
5. Apply intended Group Policy settings to the appropriate users or computers.
6. Restrict administrative access to authorized accounts.
7. Support documented troubleshooting and recovery procedures.

### DNS Requirements

Domain-joined clients must use DNS infrastructure capable of resolving the Active Directory domain and its service records.

Configure client DNS settings to point to the appropriate internal DNS server.

External name resolution should be handled through DNS forwarding or another appropriate internal DNS configuration.

Do not configure public DNS servers directly as alternate DNS servers on domain clients as a workaround for internal DNS issues.

---

## 5. Active Directory Organizational Structure

### Business Requirement

The company needs an organized directory structure that supports account administration, workstation management, and targeted Group Policy deployment.

The structure should be easy for a single administrator to understand and maintain.

### Proposed OU Structure

```text
ad.comics.internal
│
├── OU=PanelAndPixel
│   │
│   ├── OU=Users
│   │   ├── OU=Management
│   │   ├── OU=Retail
│   │   ├── OU=Publishing
│   │   └── OU=IT
│   │
│   ├── OU=Workstations
│   │   ├── OU=Management
│   │   ├── OU=Retail
│   │   ├── OU=Publishing
│   │   └── OU=IT
│   │
│   ├── OU=Servers
│   │
│   └── OU=Administrative Accounts
│
└── Default Active Directory containers
```

The proposed structure should be adapted to the existing directory.

Service accounts do not need their own OU until the lab introduces services that require them.

### OU Requirements

- Separate user objects from computer objects.
- Organize users by department.
- Organize workstations according to the intended policy design.
- Maintain a clear location for privileged administrative accounts.
- Keep member servers organized separately from ordinary workstations.
- Document the purpose of each OU.
- Avoid unnecessary changes to default Active Directory containers.

### Validation

Demonstrate that:

- Users are placed in the appropriate OUs.
- Workstations are placed in the intended computer OUs.
- Department-specific policies can target the appropriate workstation OUs.
- Moving a computer to another OU changes its applicable policy scope where expected.
- OU placement alone does not unintentionally grant resource permissions.

---

## 6. User Accounts and Identity Management

### Business Requirement

All employees require individually identifiable accounts.

The company needs a repeatable process for creating accounts, updating employee information, modifying access, and disabling accounts when employment ends.

### Representative Employee Accounts

Create only enough accounts to demonstrate the requirements.

Suggested test population:

| Username | Display Name | Department | Purpose |
|---|---|---|---|
| `owner` | Alex Morgan | Management | Business owner and management access testing |
| `retail1` | Taylor Reed | Retail | Standard retail employee |
| `retail2` | Jordan Blake | Retail | Additional retail employee for group testing |
| `creative1` | Casey Parker | Publishing | Publishing and creative resource access |
| `creative2` | Riley Bennett | Publishing | Additional creative employee for access testing |
| `helpdesk1` | Sam Ellis | IT | Delegated account-management testing |
| `admin-kevin` | Lab Administrator | IT | Privileged administrative operations |

These are fictional accounts for the lab. The names and usernames can be changed to suit your preferred naming convention.

The account `admin-kevin` is an example privileged account name, not a recommendation to use a particular person's real name in a public repository.

The existing domain administrator account may be used to establish the initial environment. Create a separate administrative account only where appropriate, and do not remove or disable essential built-in administrative access without a verified recovery plan.

**Important:** The business owner does not automatically need Domain Admin privileges. Management's business responsibilities should not be confused with infrastructure administration.

### Account Naming Convention

Establish a documented username standard.

For this lab, use a simple convention such as:

`firstname.lastname`

or a short username convention such as:

`firstinitiallastname`

The implementation must account for duplicate names and naming conflicts.

### Account Provisioning Requirements

When creating an employee account, the administrator must:

1. Identify the employee's department.
2. Create the account using the naming convention.
3. Populate the display name and relevant directory attributes.
4. Place the account in the correct OU.
5. Add the account to the appropriate security groups.
6. Configure initial credentials securely.
7. Verify that the account can authenticate.
8. Confirm that the account can access the required resources.
9. Confirm that access to restricted resources is denied.

### Account Offboarding Requirements

When an employee leaves the business:

1. Disable the account promptly.
2. Review and revoke access as appropriate.
3. Review the account's group memberships.
4. Identify business files that must be retained.
5. Transfer ownership or access to retained data according to business requirements.
6. Document the action.
7. Delete or archive the account only when appropriate.

### Validation

Demonstrate the lifecycle using a disposable test account.

Create the account, verify authentication, modify its group membership, disable it, and verify that new domain logons are denied.

---

## 7. Security Groups and Role-Based Access

### Business Requirement

Employees must receive access based on their job responsibilities.

The administrator should be able to change resource access through group membership rather than editing the permissions of individual users.

### Proposed Security Groups

#### Department Membership Groups

| Group | Purpose |
|---|---|
| `GG_Management` | Management employees |
| `GG_Retail` | Retail employees |
| `GG_Publishing` | Publishing and creative employees |
| `GG_IT` | IT personnel |

#### Resource Permission Groups

| Group | Purpose |
|---|---|
| `DL_FS_Management_Modify` | Modify access to Management files |
| `DL_FS_Retail_Modify` | Modify access to Retail files |
| `DL_FS_Publishing_Modify` | Modify access to Publishing files |
| `DL_FS_IT_Modify` | Modify access to IT files |
| `DL_FS_Public_Read` | Read access to shared company resources |

Additional read-only groups can be introduced when a business requirement calls for different access levels.

### Group Design

Use the AGDLP approach where appropriate:

- **Accounts:** Individual employees.
- **Global groups:** Departmental membership.
- **Domain Local groups:** Resource-specific permissions.
- **Permissions:** Access rights assigned to files and folders.

Example:

```text
creative1
    |
    v
GG_Publishing
    |
    v
DL_FS_Publishing_Modify
    |
    v
\\FILE01\Publishing$
```

This structure allows employees to change departments without requiring the administrator to redesign every resource ACL.

### Requirements

- Use security groups to manage resource access.
- Avoid assigning file permissions directly to individual employees.
- Keep departmental membership groups separate from resource permission groups.
- Document the purpose of each group.
- Review memberships when employee responsibilities change.
- Avoid unnecessary administrative group memberships.

### Validation

Use the representative employee accounts to confirm that:

- Publishing employees can access publishing files.
- Retail employees can access retail files.
- Employees cannot access restricted departmental folders without authorization.
- Changes to group membership affect resource access as intended.
- The IT account has only the administrative access required for its responsibilities.

---

## 8. File Server and Shared Storage

### Business Requirement

Panel & Pixel Comics relies on shared files for daily business operations.

The company needs a centralized location for departmental documents, unpublished creative work, operational records, and common resources.

The file server must protect business data while allowing employees to collaborate.

### File Server Requirements

Configure `FILE01` as a domain-joined member server.

The server must:

- Host departmental file shares.
- Support SMB access from domain-joined clients.
- Use group-based permissions.
- Apply appropriate NTFS access controls.
- Restrict sensitive business information.
- Provide a common area for approved shared resources.
- Support permission testing and troubleshooting.

### Proposed Directory Structure

```text
D:\Shares
│
├── Management
├── Retail
├── Publishing
├── IT
└── Public
```

Example network paths:

```text
\\FILE01\Management$
\\FILE01\Retail$
\\FILE01\Publishing$
\\FILE01\IT$
\\FILE01\Public
```

The dollar sign hides a share from ordinary network browsing but does not secure the underlying data.

### Access Requirements

| Resource | Management | Retail | Publishing | IT |
|---|---|---|---|---|
| Management | Modify | Denied | Denied | Authorized administration |
| Retail | Denied | Modify | Denied | Authorized administration |
| Publishing | Denied | Denied | Modify | Authorized administration |
| IT | Denied | Denied | Denied | Authorized access |
| Public | Read | Read | Read | Authorized administration |

The table defines the baseline access model.

Any exception must be justified by a business requirement. For example, management may need access to certain business reports without receiving unrestricted access to all publishing content.

### Permission Requirements

- Configure SMB share permissions and NTFS permissions.
- Use security groups for access control.
- Apply least privilege.
- Avoid broad permissions such as granting Everyone Full Control.
- Verify permission inheritance.
- Document the intended access for each share.
- Test both permitted and denied operations.
- Avoid storing real business or personal data in the lab.

### Validation

For each share:

1. Connect using an authorized account.
2. Create a test file where permitted.
3. Read and modify files according to the intended permissions.
4. Connect using an unauthorized account.
5. Confirm that restricted access is denied.
6. Review share and NTFS permissions if the result differs from expectations.
7. Record the results.

---

## 9. Group Policy and Workstation Security

### Business Requirement

The company needs consistent security settings across Windows workstations without manually configuring each machine.

Group Policy will be used to apply common settings and demonstrate how workstation configurations can be centrally managed.

### Proposed GPOs

| GPO | Scope | Purpose |
|---|---|---|
| `GPO-Workstation-Baseline` | Workstation OUs | Apply common security settings |
| `GPO-Retail-Workstations` | Retail workstation OU | Apply appropriate retail-specific settings |
| `GPO-Publishing-Workstations` | Publishing workstation OU | Apply appropriate creative workstation settings |
| `GPO-IT-Workstations` | IT workstation OU | Apply approved IT workstation settings |

Domain account password and lockout policies should be configured according to the intended domain policy design rather than duplicated indiscriminately across workstation GPOs.

### Security Settings

Evaluate and configure suitable settings, including:

- Screen-lock timeout.
- Interactive logon requirements.
- Windows Firewall.
- Appropriate Windows security options.
- Restrictions on unnecessary local administrator access.
- Relevant security auditing.
- Other baseline settings appropriate for the Windows versions in the lab.

Settings should be selected carefully to avoid blocking legitimate administrative tasks.

### Group Policy Requirements

Demonstrate an understanding of:

- User Configuration versus Computer Configuration.
- GPO linking and OU targeting.
- Inheritance and precedence.
- Security filtering.
- Policy refresh.
- Effective policy reporting.
- Troubleshooting settings that fail to apply.

Useful commands include:

```powershell
gpupdate /force
gpresult /r
gpresult /h C:\Temp\gpresult.html
```

### Validation

Demonstrate that:

1. A domain-joined workstation receives the common baseline.
2. A workstation in the Publishing OU receives the intended additional policy.
3. A workstation outside the Publishing OU does not receive that OU-specific policy unless another link or policy scope applies.
4. The administrator can identify the policies that are actually applied.
5. A deliberately misconfigured policy can be diagnosed and corrected.

Document the observed results rather than relying solely on the settings displayed in the Group Policy editor.

---

## 10. Delegated Administration and Privileged Accounts

### Business Requirement

The company has a small IT team and wants routine support tasks to be performed without granting every technician unrestricted control over the domain.

The administrator must establish a clear separation between ordinary employee accounts, help desk accounts, and privileged administrative access.

### Administrative Roles

#### Help Desk Account

The help desk account should be able to perform approved support tasks, such as:

- Resetting passwords for designated users.
- Unlocking accounts.
- Viewing relevant user attributes.
- Performing routine account-management tasks within the assigned OU.

The account should not automatically receive Domain Admin privileges.

#### Systems Administrator Account

The privileged systems administrator account should be used for tasks such as:

- Managing Active Directory.
- Configuring DNS.
- Managing Group Policy.
- Administering domain-joined servers.
- Troubleshooting infrastructure.
- Performing approved security and recovery operations.

### Requirements

- Create an appropriate help desk security group.
- Delegate only the necessary permissions.
- Restrict delegation to the intended OU where possible.
- Use separate accounts for privileged operations.
- Avoid using privileged accounts for routine employee activities.
- Document the intended permissions.
- Validate the delegation using a test account.

### Validation

Using the help desk account:

1. Reset a test user's password.
2. Unlock a test account.
3. Attempt an administrative action outside the delegated scope.
4. Verify that the permitted actions succeed.
5. Verify that unauthorized actions are denied.

Document the results and explain how the delegation limits administrative exposure.

---

## 11. DNS and Domain Troubleshooting

### Business Requirement

Employees depend on reliable domain authentication, workstation connectivity, and access to internal resources.

The administrator must be able to diagnose common infrastructure problems without relying on trial and error.

### Required Troubleshooting Scenarios

#### Scenario A: Domain Login Failure

A user cannot authenticate to a domain-joined workstation.

Investigate:

- Account status.
- Credential correctness.
- Network connectivity.
- Client DNS configuration.
- Domain controller discovery.
- Time synchronization.
- Relevant Windows event logs.

Document the root cause and validate the fix.

#### Scenario B: DNS Failure

A workstation cannot locate a domain controller or resolve an internal server name.

Investigate:

- DNS client configuration.
- DNS server availability.
- Name resolution using `nslookup`.
- Active Directory service records.
- Network connectivity.
- DNS forwarding where external resolution is involved.

Do not simply replace internal DNS with a public resolver.

#### Scenario C: File Share Access Failure

A user receives an access-denied error when accessing a shared folder.

Investigate:

- User group membership.
- Share permissions.
- NTFS permissions.
- Permission inheritance.
- Network connectivity.
- Existing SMB sessions.
- Whether a recent group membership change requires a fresh logon.

#### Scenario D: Group Policy Failure

A workstation does not receive an expected configuration.

Investigate:

- OU placement.
- GPO links.
- User versus computer settings.
- Security filtering.
- Policy precedence.
- Domain connectivity.
- Group Policy operational logs.

Use:

```powershell
gpupdate /force
gpresult /h C:\Temp\gpresult.html
```

#### Scenario E: Account Lockout

A user's account repeatedly becomes locked.

Investigate:

- Account lockout status.
- Relevant security events.
- Stale credentials.
- Saved credentials or mapped drives.
- Scheduled tasks or services using the account, where applicable.
- Whether the lockout policy is configured as intended.

### Troubleshooting Documentation

For each scenario, record:

1. Reported problem.
2. Expected behavior.
3. Initial observations.
4. Diagnostic commands.
5. Evidence collected.
6. Root cause.
7. Corrective action.
8. Verification results.
9. Preventive recommendations.

The objective is to demonstrate a repeatable troubleshooting process.

---

## 12. PowerShell Administration and Automation

### Business Requirement

A single systems administrator cannot afford to perform every routine administrative task manually.

The company needs simple, repeatable scripts that reduce errors and improve the consistency of account administration.

### Required Automation

Implement at least two useful PowerShell scripts, with additional scripts added as time permits.

#### Script 1: Bulk User Provisioning

Create a script that reads employee information from a CSV file.

Example:

```csv
FirstName,LastName,Username,Department,Title
Taylor,Reed,treed,Retail,Sales Associate
Casey,Parker,cparker,Publishing,Illustrator
Jordan,Blake,jblake,Retail,Inventory Associate
```

The script should:

- Validate required fields.
- Check for existing usernames.
- Verify the target OU.
- Create approved accounts.
- Populate relevant directory attributes.
- Assign appropriate group memberships.
- Log successes and failures.
- Report invalid or duplicate records.

The script must not silently skip errors or create accounts using predictable passwords embedded in the source code.

Use synthetic input data and a controlled initial credential process.

#### Script 2: User Account Audit

Generate a report containing:

- Username.
- Display name.
- Department.
- Account status.
- Organizational Unit.
- Selected security group memberships.

Use the report to identify accounts that are disabled, misplaced, or inconsistent with the intended directory design.

#### Script 3: Security Group Audit

Export the membership of selected departmental and resource permission groups.

The report should help the administrator verify that users receive only the access intended for their roles.

### Automation Standards

Scripts must:

- Use clear naming conventions.
- Include comments where useful.
- Validate input before making changes.
- Handle errors.
- Generate meaningful output.
- Require appropriate privileges.
- Avoid storing credentials in source files.
- Be tested against disposable lab accounts.
- Include execution instructions in the repository.

### Validation

Demonstrate that a script can process valid input, identify invalid data, detect duplicate accounts, and report errors.

Document the expected output and provide a sanitized example report.

---

## 13. Security Auditing and Event Logs

### Business Requirement

The company needs sufficient visibility into administrative changes and account activity to investigate problems and review important security events.

### Audit Requirements

Configure appropriate auditing for selected events, including:

- User account creation.
- User account disablement.
- Security group membership changes.
- Privileged group membership changes.
- Relevant logon failures.
- Account lockouts.
- Group Policy modifications where appropriate.

### Validation

Perform selected administrative actions and inspect the relevant Windows event logs.

For example:

1. Create a test account.
2. Add the account to a security group.
3. Disable the account.
4. Review the relevant event logs.
5. Identify the event details available for investigation.
6. Document the result.

Audit settings must be configured where required; do not assume every event is logged by default.

### Deliverables

Document:

- The selected audit settings.
- The systems where relevant events are recorded.
- How to locate the events.
- The information they provide.
- How an administrator could use the information to investigate a problem.

A centralized logging platform is a potential future enhancement, not a requirement for the initial project.

---

## 14. Backup and Recovery

### Business Requirement

The company relies on its shared files for daily operations.

The administrator must have a documented process for recovering important files and protecting the domain infrastructure against accidental changes or failures.

### Requirements

Develop a basic recovery strategy covering:

- File server data.
- Active Directory System State.
- Important configuration documentation.
- Recovery of accidentally deleted or modified test objects.
- Verification that backups are usable.

### Practical Exercises

1. Back up a test directory containing sample company files.
2. Restore a deleted test file.
3. Verify that the restored file contains the expected data.
4. Document the backup destination and procedure.
5. Investigate supported options for recovering a deleted Active Directory object.
6. Document the difference between file recovery and Active Directory recovery.

Use a supported backup method appropriate for the installed Windows Server version.

A virtual machine snapshot can be useful as a lab checkpoint but should not be treated as a complete production backup strategy.

### Validation

Complete at least one successful recovery exercise.

Record:

- What was backed up.
- What was deleted or changed.
- Which recovery procedure was used.
- How the restored data was verified.
- What limitations remain in the lab's recovery strategy.

---

## 15. Operational Documentation

### Business Requirement

The company needs documented IT procedures so that routine tasks can be performed consistently and future support personnel can understand the environment.

### Required Runbooks

Create concise, repeatable procedures for:

| Runbook | Purpose |
|---|---|
| User onboarding | Create accounts and assign access |
| User offboarding | Disable accounts and review access |
| Password reset | Reset credentials and verify account status |
| Account lockout | Diagnose and resolve lockouts |
| Domain joining | Join a workstation to the domain |
| File share provisioning | Create shares and configure permissions |
| Group Policy troubleshooting | Diagnose policy-processing issues |
| DNS troubleshooting | Investigate internal name-resolution failures |
| Backup recovery | Restore and verify selected data |

Each runbook should include:

- Purpose.
- Prerequisites.
- Required permissions.
- Procedure.
- Verification steps.
- Common failure scenarios.

Use screenshots and sanitized command output where they help explain the process.

Never publish passwords, recovery secrets, private keys, or sensitive configuration information.

---

## 16. Implementation Plan

The project will be implemented in stages to keep the environment manageable.

### Phase 1: Infrastructure Validation

- Document the existing domain configuration.
- Verify Active Directory and DNS.
- Document the server IP configuration.
- Confirm that a Windows client can locate a domain controller.
- Establish the initial infrastructure diagram.

**Deliverables:** Infrastructure overview, domain configuration notes, and connectivity test results.

### Phase 2: Directory Design

- Create the proposed OU structure.
- Establish user and computer naming conventions.
- Create representative employee accounts.
- Create departmental security groups.
- Document account lifecycle procedures.

**Deliverables:** OU hierarchy, user and group inventory, and account management documentation.

### Phase 3: File Services

- Configure the domain-joined file server.
- Create departmental shares.
- Configure share and NTFS permissions.
- Implement group-based access control.
- Test authorized and unauthorized access.

**Deliverables:** File server documentation, access matrix, and permission test results.

### Phase 4: Group Policy

- Implement the workstation security baseline.
- Configure appropriate password and account lockout policies.
- Create department-specific policies where needed.
- Validate policy targeting and inheritance.
- Document the effective policy results.

**Deliverables:** GPO inventory, configuration notes, and policy test evidence.

### Phase 5: Delegated Administration

- Create the help desk security group.
- Delegate appropriate permissions.
- Establish separate privileged accounts where appropriate.
- Test permitted and prohibited operations.

**Deliverables:** Delegation documentation and validation results.

### Phase 6: Automation and Troubleshooting

- Develop the user provisioning script.
- Create an account or group auditing script.
- Complete the required troubleshooting scenarios.
- Document diagnostic commands and fixes.

**Deliverables:** PowerShell scripts, sample CSV data, and troubleshooting runbooks.

### Phase 7: Auditing and Recovery

- Configure selected audit policies.
- Validate important administrative events.
- Document the backup strategy.
- Complete a recovery exercise.

**Deliverables:** Audit documentation, event investigation, and recovery test results.

### Phase 8: Final Validation

- Review each project requirement.
- Correct configuration issues.
- Capture sanitized evidence.
- Verify that the documentation matches the implemented environment.
- Prepare the repository for public review.

**Deliverables:** Final README, infrastructure diagrams, scripts, runbooks, and requirements validation matrix.

---

## 17. Acceptance Criteria

The project is complete when the implemented functionality has been tested and documented.

| ID | Requirement | Evidence |
|---|---|---|
| AC-01 | Active Directory is operational | Successful domain authentication |
| AC-02 | DNS supports domain discovery | DNS queries and domain controller discovery |
| AC-03 | Directory objects follow the documented structure | OU and object inventory |
| AC-04 | User lifecycle procedures work | Account provisioning and disablement tests |
| AC-05 | Security groups control resource access | Group membership and access tests |
| AC-06 | File shares enforce departmental restrictions | Successful and denied access tests |
| AC-07 | Group Policy applies intended settings | Effective policy reports |
| AC-08 | Delegated administration follows least privilege | Permitted and prohibited action tests |
| AC-09 | Common infrastructure failures can be diagnosed | Completed troubleshooting records |
| AC-10 | PowerShell automation is repeatable | Tested scripts and sample output |
| AC-11 | Important administrative events can be investigated | Event log evidence |
| AC-12 | Recovery procedures work | Successful restoration exercise |
| AC-13 | Administrative tasks are documented | Completed runbooks |
| AC-14 | The project is understandable to another administrator | README, diagrams, and supporting documentation |

A requirement must not be marked complete until its behavior has been verified.

---

## 18. GitHub Repository Structure

Organize the repository so that hiring managers can quickly understand the environment and technical reviewers can explore the implementation.

```text
active-directory-small-business-lab/
│
├── README.md
│
├── docs/
│   ├── architecture.md
│   ├── active-directory-design.md
│   ├── network-design.md
│   ├── group-policy.md
│   ├── file-server-permissions.md
│   ├── delegated-administration.md
│   ├── backup-and-recovery.md
│   │
│   ├── runbooks/
│   │   ├── user-onboarding.md
│   │   ├── user-offboarding.md
│   │   ├── password-reset.md
│   │   ├── domain-join.md
│   │   ├── file-share-access.md
│   │   └── troubleshooting.md
│   │
│   └── troubleshooting/
│       ├── dns-failure.md
│       ├── authentication-failure.md
│       ├── group-policy-failure.md
│       └── file-share-access-denied.md
│
├── scripts/
│   ├── New-LabUsers.ps1
│   ├── Get-LabUserAudit.ps1
│   └── Export-LabGroupMembers.ps1
│
├── samples/
│   └── users.example.csv
│
├── diagrams/
│   ├── infrastructure.mmd
│   └── active-directory-structure.mmd
│
├── evidence/
│   ├── domain-connectivity/
│   ├── group-policy/
│   ├── file-permissions/
│   ├── delegation/
│   └── recovery/
│
└── .gitignore
```

Only include files that have been created or clearly document planned work.

Evidence should demonstrate actual configuration and validation without exposing credentials, private keys, or sensitive network details.

---

## 19. Future Enhancements

After completing the initial environment, consider expanding the lab with additional infrastructure and administration challenges.

### Active Directory Resilience

- Deploy a second domain controller.
- Configure and validate replication.
- Investigate DNS replication.
- Simulate the unavailability of a domain controller.

### Windows Server Administration

- Explore server update management.
- Implement more comprehensive backup procedures.
- Monitor resource utilization and service availability.
- Document server maintenance procedures.

### Security Improvements

- Explore Windows LAPS for local administrator password management.
- Review privileged group membership.
- Improve Windows security auditing.
- Evaluate workstation security baselines.
- Explore managed service accounts where appropriate.

### Automation

- Expand PowerShell provisioning.
- Automate account and group audits.
- Generate infrastructure inventory reports.
- Create repeatable configuration and validation procedures.

### Monitoring

- Centralize selected Windows event logs.
- Implement infrastructure health checks.
- Create a basic monitoring dashboard.
- Document incident investigation procedures.

Each enhancement should be added only after the foundational environment is working and documented.

---

## 20. Project Outcome

This project demonstrates the design and administration of a small-business Windows infrastructure environment built around Active Directory Domain Services.

The environment addresses practical business requirements involving centralized identity management, departmental file storage, access control, workstation security, delegated administration, PowerShell automation, troubleshooting, auditing, and recovery planning.

The lab emphasizes the relationship between infrastructure configuration and operational outcomes.

By completing the project, the administrator demonstrates the ability to configure and maintain core Windows infrastructure, investigate common technical failures, enforce appropriate access controls, automate repetitive tasks, and document procedures that can be followed by other IT personnel.

**The objective is to demonstrate the practical skills required to support a small organization's Windows environment as its systems administrator, not simply to demonstrate that Active Directory has been installed.**
