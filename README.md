# Stark Panels & Publishing — Active Directory Lab

A hands-on Windows Server and Active Directory lab demonstrating practical IT infrastructure, systems administration, identity management, access control, Group Policy, file services, troubleshooting, and automation skills.

---

## 1. Project Overview

This project simulates the IT infrastructure of a small business using Windows Server, Active Directory Domain Services (AD DS), DNS, Group Policy, and Windows client machines.

The objective is to build and administer a centralized Windows domain environment that supports employee accounts, workstation management, departmental file storage, and controlled administrative access.

Rather than configuring Active Directory in isolation, this lab focuses on common responsibilities performed by IT support technicians, systems administrators, and infrastructure engineers.

### Skills Demonstrated

- Active Directory Domain Services administration
- Organizational Unit (OU) design and management
- User account lifecycle management
- Security group design and access control
- Group Policy configuration and troubleshooting
- DNS and domain connectivity troubleshooting
- SMB file sharing and NTFS permissions
- Delegated administration and least privilege
- PowerShell administration and automation
- Windows security auditing and Event Viewer
- Backup, recovery, and operational documentation
- Technical documentation and infrastructure design

### Technologies

| Technology | Purpose |
|---|---|
| Windows Server | Hosts the domain controller and lab file services |
| Active Directory Domain Services | Centralized identity and domain management |
| DNS | Domain name resolution and service discovery |
| Group Policy | Centralized configuration and security enforcement |
| Windows client machines | Simulate employee workstations |
| SMB | Network file sharing |
| NTFS permissions | File and folder access control |
| PowerShell | Administrative automation and reporting |
| Event Viewer | Troubleshooting and security auditing |
| RSAT | Remote administration tools, if installed on a client |

---

## 2. Business Scenario

### Company: Stark Panels & Publishing

Stark Panels & Publishing is a fictional comic book publishing and retail company with approximately 20 employees.

The company has several departments that need access to different files, applications, and administrative resources.

As the company grows, managing individual computers and user accounts becomes increasingly difficult. The organization needs a centralized infrastructure to manage identities, enforce security policies, and control access to company resources.

### Organizational Structure

| Department | Employees | Responsibilities |
|---|---:|---|
| Management | 2 | Business operations and company oversight |
| Retail Operations | 8 | Retail locations, sales operations, and inventory |
| Publishing & Creative | 7 | Comic production, artwork, and publishing |
| IT & Administration | 3 | Technical support and internal administration |
| **Total** | **20** | |

The lab will use a small representative set of accounts rather than creating all 20 employee accounts. This keeps the environment manageable while still demonstrating the administrative tasks required in a larger organization.

### Business Requirements

The company needs to:

1. Centralize employee authentication through Active Directory.
2. Organize users and computers into a maintainable directory structure.
3. Apply consistent security settings to domain-joined computers.
4. Restrict access to departmental files based on job responsibilities.
5. Allow IT support personnel to perform approved administrative tasks without granting unrestricted domain privileges.
6. Automate repetitive administrative tasks.
7. Investigate authentication, DNS, Group Policy, and file-access problems.
8. Maintain operational documentation and demonstrate recovery procedures.

### Project Objective

Build a working lab that demonstrates how a small business can use Windows infrastructure to manage employees, computers, permissions, and routine IT operations.

---

## 3. Project Scope and Infrastructure Constraints

This lab is designed to run locally on a laptop using the available virtual machines and hardware.

### Current Environment

| Component | Configuration |
|---|---|
| Domain | `ad.comics.internal` |
| Primary server | `DC01` — logical hostname used in this documentation |
| Server roles | AD DS, DNS, and file services |
| Client machines | `CLIENT01` and `CLIENT02` |
| Directory services | Active Directory |
| File sharing | SMB shares with NTFS permissions |
| Administration | Server management tools and optional RSAT |
| Workstations | Two Windows client machines |

The hostname `DC01` is a logical name used throughout this README. If the actual server hostname differs, substitute the correct hostname in commands, paths, and configuration.

### Lab Constraints

The environment uses a single Windows Server for both domain controller and file server responsibilities.

This is a deliberate lab constraint, not a recommended production design.

Combining these roles allows the project to demonstrate domain administration and file services without requiring additional server hardware or licenses.

The environment does not currently include:

- A dedicated administrative workstation.
- A secondary domain controller.
- A separate file server.
- A separate server for monitoring or centralized logging.

These can be introduced as future enhancements.

### Production Considerations

In a production environment, domain controllers should generally not host general-purpose file shares or unrelated application workloads.

Separating these roles helps reduce the impact of a compromised service, limits administrative exposure, and allows each server role to be maintained independently.

A production environment would also require appropriate backup, recovery, availability, and monitoring arrangements.

---

## 4. Infrastructure Architecture

### Logical Architecture

```text
                    Stark Panels & Publishing
                               |
                               |
                    ad.comics.internal
                               |
                       +---------------+
                       |     DC01      |
                       |---------------|
                       | AD DS         |
                       | DNS           |
                       | File Services |
                       +---------------+
                               |
                  +------------+------------+
                  |                         |
           +-------------+           +-------------+
           |  CLIENT01   |           |  CLIENT02   |
           |-------------|           |-------------|
           | Windows     |           | Windows     |
           | Workstation |           | Workstation |
           +-------------+           +-------------+
```

### Component Responsibilities

**DC01 — Domain Controller and File Server**

- Hosts Active Directory Domain Services.
- Provides DNS for the Active Directory domain.
- Authenticates domain users and computers.
- Stores the directory objects used by the lab.
- Hosts departmental SMB file shares.
- Provides a platform for testing domain administration and file access.

**CLIENT01 — Employee Workstation**

- Joins the Active Directory domain.
- Tests user authentication.
- Receives applicable Group Policy settings.
- Tests access to departmental resources.

**CLIENT02 — Employee Workstation**

- Joins the Active Directory domain.
- Tests a different user's access permissions.
- Helps verify that access restrictions work across multiple client machines.

### DNS Configuration

Domain-joined clients should use the internal DNS server that can resolve the Active Directory domain and its service records.

For this lab, that normally means configuring clients to use the DNS service hosted on `DC01`.

Do not configure a public DNS server as a direct alternate DNS server on domain clients. Public DNS servers do not know the private Active Directory DNS records needed for domain discovery and authentication.

If internet name resolution is required, configure appropriate DNS forwarding on the internal DNS server.

### Example Domain Information

| Setting | Example |
|---|---|
| Domain FQDN | `ad.comics.internal` |
| NetBIOS domain name | `AD` |
| Domain controller hostname | `DC01` |
| Domain controller FQDN | `DC01.ad.comics.internal` |
| Example client | `CLIENT01.ad.comics.internal` |
| Example client | `CLIENT02.ad.comics.internal` |

The NetBIOS name should be verified in the actual domain configuration rather than assumed if the domain was created differently.

---

## 5. Active Directory OU Structure

The Active Directory environment uses Organizational Units (OUs) to organize directory objects by object type, location, and department.

This design supports consistent Group Policy application, easier administration, and future expansion.

### OU Hierarchy

The current OU hierarchy is organized as follows:

```text
ad.comics.internal
|
+-- Computers
|   |
|   +-- Workstations
|   |   |
|   |   +-- <Location>
|   |
|   +-- Servers
|       |
|       +-- <Location>
|
+-- Users
    |
    +-- <Location>
        |
        +-- Departments
            |
            +-- Management
            |
            +-- Retail Operations
            |
            +-- Publishing & Creative
            |
            +-- IT & Administration
```

**Note:** Laptops are not currently included in the hierarchy. A `Laptops` OU can be added later if needed.

The `<Location>` entries represent the location OUs that exist in the actual environment. Replace the placeholder with the exact OU name when documenting a specific distinguished name.

### Computer OU Design

| OU | Purpose |
|---|---|
| `Computers` | Parent OU for computer objects managed through the custom OU structure. |
| `Workstations` | Contains employee desktop computers and other workstation devices. |
| `Servers` | Contains server computer objects. |
| `<Location>` under `Workstations` | Organizes workstation computer accounts by location. |
| `<Location>` under `Servers` | Organizes server computer accounts by location. |

### User OU Design

| OU | Purpose |
|---|---|
| `Users` | Parent OU for employee user accounts. |
| `<Location>` under `Users` | Organizes employee accounts by location. |
| `Departments` | Groups employees by their business department. |
| Department OUs | Provide a place to apply department-specific user policies where appropriate. |

### Example Computer Placement

Assuming the location OU is named `Seattle`, the distinguished name for a workstation OU would be:

```text
OU=Seattle,OU=Workstations,OU=Computers,DC=ad,DC=comics,DC=internal
```

The equivalent server OU would be:

```text
OU=Seattle,OU=Servers,OU=Computers,DC=ad,DC=comics,DC=internal
```

These examples assume that `Computers`, `Workstations`, `Servers`, and the location entries are OUs in Active Directory.

### OU Design Considerations

**Separate workstations and servers**

Workstations and servers have different security and operational requirements. Separating them allows different Group Policy settings to be applied where appropriate.

**Organize computers by location**

Location-based OUs allow the directory structure to expand as the organization adds offices or facilities.

**Keep user and computer objects separate**

User accounts and computer accounts have different management and policy requirements. Separate hierarchies help administrators manage each object type independently.

**Use security groups for resource access**

OU placement primarily supports directory organization, delegation, and Group Policy application. Security groups should generally control access to file shares and other resources.

**Avoid unnecessary OU complexity**

An OU should exist to support a meaningful administrative, delegation, or policy requirement. The structure should remain easy for administrators to understand.

### Important: Default Computer Container

Active Directory's built-in `Computers` location is a **container**, not an Organizational Unit.

Your custom `OU=Computers` is different from the built-in `CN=Computers` container.

Newly domain-joined computers may initially appear in the default container unless the domain's default computer container has been redirected or another placement method is used.

For this lab, computers can be moved manually into the appropriate location under `Workstations` or `Servers`.

Verify that each computer object is in the intended OU before testing Group Policy.

### Validation

The following should be verified:

- User accounts are placed in their appropriate department OUs.
- Client computer objects are placed under `Computers → Workstations → Location`.
- Server computer objects are placed under `Computers → Servers → Location`.
- The intended Group Policy Objects are linked to the correct OUs.
- Users and computers receive the expected policies.

---

## 6. User Account Lifecycle Management

User account management is one of the most common responsibilities in an Active Directory environment.

This section demonstrates how accounts are created, maintained, disabled, and reviewed throughout the employee lifecycle.

### Representative Test Accounts

The lab uses a small number of representative employee accounts.

| Account | Department | Purpose |
|---|---|---|
| `owner` | Management | Tests management-level access |
| `retail1` | Retail Operations | Tests retail permissions |
| `retail2` | Retail Operations | Tests access consistency between employees |
| `creative1` | Publishing & Creative | Tests publishing resource access |
| `creative2` | Publishing & Creative | Tests publishing resource access |
| `helpdesk1` | IT & Administration | Tests delegated support tasks |

These are lab account names. They should not be used as real employee credentials.

Create a separate privileged administrative account for domain administration rather than using an ordinary employee account for administrative tasks.

### Account Provisioning

When a new employee joins the organization, the administrator should:

1. Confirm the employee's identity and department.
2. Create the user account with an approved naming convention.
3. Place the account in the correct location and department OU.
4. Add the account to the appropriate security groups.
5. Configure the account's initial password using a secure process.
6. Verify account status and required attributes.
7. Test authentication.
8. Verify access to the resources required for the employee's role.

### Account Modification

When an employee changes departments or responsibilities:

1. Review the employee's existing group memberships.
2. Remove memberships that are no longer required.
3. Add the required new memberships.
4. Update relevant account attributes.
5. Verify the employee's access to required resources.
6. Confirm that access to previous departmental resources has been removed where appropriate.

### Account Offboarding

When an employee leaves the organization:

1. Disable the account promptly.
2. Revoke or terminate other active access mechanisms where applicable.
3. Review the employee's group memberships.
4. Transfer ownership of business files where required.
5. Preserve business data according to company policy.
6. Document the action and its completion.
7. Delete the account only when permitted by the organization's retention and recovery requirements.

Disabling an account is generally preferable to immediately deleting it because it preserves the account object for investigation, recordkeeping, and recovery.

### Validation

Demonstrate that:

- A newly created user can authenticate.
- A user receives the correct group memberships.
- A modified user receives the correct access after a department change.
- A disabled user cannot authenticate normally.
- Account changes can be investigated through available directory information and security logs.

---

## 7. Security Groups and Resource Access

Security groups provide a manageable way to assign permissions to employees based on their job responsibilities.

Instead of assigning permissions individually to each employee, the administrator assigns users to groups and grants permissions to those groups.

### Group Design

The lab uses two general categories of security groups:

1. **Global groups:** Represent users with common job responsibilities.
2. **Domain Local groups:** Represent access to resources within the domain.

This follows the AGDLP model:

**Accounts → Global Groups → Domain Local Groups → Permissions**

### Proposed Global Groups

| Group | Purpose |
|---|---|
| `GG_Management` | Management employees |
| `GG_Retail` | Retail Operations employees |
| `GG_Publishing` | Publishing & Creative employees |
| `GG_IT` | IT & Administration employees |

The `GG_` prefix identifies a Global security group.

### Proposed Domain Local Groups

| Group | Purpose |
|---|---|
| `DL_FS_Management_Modify` | Modify access to management files |
| `DL_FS_Retail_Modify` | Modify access to retail files |
| `DL_FS_Publishing_Modify` | Modify access to publishing files |
| `DL_FS_IT_Modify` | Modify access to IT files |
| `DL_FS_Public_Modify` | Modify access to shared company files |

The `DL_` prefix identifies a Domain Local group. The `FS_` portion indicates a file-server resource.

Additional read-only groups can be created if the business scenario requires different levels of access.

### Example Group Membership

```text
User: creative1
    |
    v
GG_Publishing
    |
    v
DL_FS_Publishing_Modify
    |
    v
Publishing folder permissions
```

An employee should receive access through the appropriate group memberships rather than through individually assigned folder permissions.

### Example Access Matrix

| Department | Management Files | Retail Files | Publishing Files | IT Files |
|---|---|---|---|---|
| Management | Modify | Denied by default | Denied by default | Denied by default |
| Retail Operations | Denied by default | Modify | Denied by default | Denied by default |
| Publishing & Creative | Denied by default | Denied by default | Modify | Denied by default |
| IT & Administration | Denied by default | Denied by default | Denied by default | Modify |

This is a starting policy, not an assumption that every real business should use these exact permissions. Any cross-department access should be explicitly justified and configured.

Membership in `GG_IT` does not automatically grant domain administrator privileges. IT support tasks should be delegated separately and according to least privilege.

### Validation

Test both authorized and unauthorized access:

- A publishing employee can modify publishing files.
- A retail employee cannot modify publishing files.
- A management employee receives the intended management access.
- An ordinary employee cannot access IT-restricted files.
- Access is updated when an employee's group membership changes.

Document the expected result and the observed result for each test.

---

## 8. File Server and Departmental Storage

The file server provides centralized storage for company documents.

SMB provides network file sharing, while NTFS permissions control access to folders and files on the server's filesystem.

Both permission layers must be configured correctly.

### Proposed Directory Structure

The following is an example directory structure on the server:

```text
D:\Shares
|
+-- Management
|
+-- Retail
|
+-- Publishing
|
+-- IT
|
+-- Public
```

The `D:` drive is an example. Use the actual data volume available in the lab.

If the server has only one suitable volume, a directory on that volume can be used instead.

### Proposed SMB Shares

| Share | Example UNC Path | Purpose |
|---|---|---|
| Management | `\\DC01\Management$` | Management documents |
| Retail | `\\DC01\Retail$` | Retail documents |
| Publishing | `\\DC01\Publishing$` | Publishing and creative files |
| IT | `\\DC01\IT$` | IT documentation and administration files |
| Public | `\\DC01\Public` | Documents intended for broader company access |

The `$` suffix hides a share from ordinary browse listings. It does **not** provide access control or replace permissions.

Access must still be controlled through SMB share permissions and NTFS permissions.

### Share and NTFS Permissions

Effective access is determined by the combined effect of SMB share permissions and NTFS permissions.

A user must be permitted by both layers to perform the requested operation over SMB.

For this lab, use a consistent permission design:

- Configure share permissions to allow the intended users or groups to connect.
- Use NTFS permissions to enforce department-specific access.
- Assign permissions to security groups rather than individual users wherever practical.
- Avoid granting broad access to `Everyone` or `Authenticated Users` without a deliberate reason.
- Verify that inherited permissions do not unintentionally grant access.

A straightforward lab design is to grant the intended authenticated users or groups sufficient share-level access and use NTFS permissions to enforce the departmental restrictions. The exact configuration should be documented.

### Example: Publishing Share

The intended access path is:

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
NTFS permissions on D:\Shares\Publishing
    |
    v
\\DC01\Publishing$
```

The share-level permissions must also permit the required operations.

### Access Testing

Use both client machines to test different user accounts.

| Test | Expected Result |
|---|---|
| `creative1` opens the Publishing share | Allowed |
| `creative1` creates or modifies an authorized test file | Allowed |
| `retail1` attempts to modify Publishing files | Denied |
| `retail1` accesses the Retail share | Allowed |
| An ordinary employee accesses IT-restricted files | Denied |
| An authorized user accesses Public files | Allowed |

Record screenshots or command output showing the results.

### Production Considerations

In production, the file server would generally be a separate server rather than the domain controller.

Separating file services from domain services reduces the impact of compromise and simplifies independent maintenance, backup, and capacity planning.

The combined role in this project is a hardware and cost constraint for the lab.

---

## 9. Group Policy and Workstation Security

Group Policy allows administrators to apply consistent configuration and security settings to domain users and computers.

The lab should demonstrate that policies are applied intentionally based on the OU structure.

### Common Workstation Baseline

Create a Group Policy Object for baseline workstation configuration.

Possible settings include:

- Screen-lock and inactivity requirements.
- Windows Defender and firewall settings.
- Security options appropriate for the client operating system.
- Restrictions on selected system settings.
- Windows Update configuration where appropriate.
- User experience settings that are useful for the lab.

Use supported security settings and document why each setting is applied.

Avoid adding policies solely for the sake of having more policies. Each setting should have a clear purpose and a way to verify its behavior.

### Department-Specific Policies

Department-specific policies can be introduced when there is a genuine requirement.

Examples:

| Policy | Example Purpose |
|---|---|
| `GPO_Workstation_Baseline` | Common workstation security settings |
| `GPO_Publishing_Workstations` | Additional settings for publishing workstations |
| `GPO_IT_Workstations` | Appropriate administrative workstation configuration |

These are proposed names. Use the names actually configured in the lab when documenting implementation.

Link computer configuration policies to the relevant computer OUs and user configuration policies to the relevant user OUs, unless a specific requirement calls for another design.

### Password and Account Lockout Policies

Password and account lockout policies should be configured with an understanding of their scope.

For a basic domain password and lockout policy, configure the appropriate settings through domain-level Group Policy.

Fine-Grained Password Policies can be used when different password or lockout requirements are needed for selected users or global security groups.

Do not assume that linking an ordinary password policy to a department OU will override the domain's default password policy for its users.

### Verify Group Policy

On a domain-joined client, run:

```powershell
gpupdate /force
```

To review the applied policies:

```powershell
gpresult /r
```

To generate an HTML report:

```powershell
gpresult /h C:\Temp\gpresult.html
```

Ensure the destination directory exists before generating the report.

If a policy does not apply, investigate:

1. Whether the computer or user is in the intended OU.
2. Whether the GPO is linked to the appropriate OU or domain.
3. Whether security filtering permits the object to apply the policy.
4. Whether inheritance, enforcement, or link order affects the result.
5. Whether DNS and domain connectivity are working.
6. Whether the policy applies to the relevant operating system and object type.
7. Whether the Group Policy event logs report errors.

### Validation

Demonstrate that:

- A client receives the intended workstation baseline.
- The relevant settings are visible in the resulting policy report.
- A policy linked to the wrong OU does not produce the expected result until placement or linkage is corrected.
- Group Policy problems can be investigated and documented.

---

## 10. Delegated Administration and Least Privilege

Not every IT employee should have Domain Admin privileges.

A well-designed environment allows support staff to perform approved tasks without granting unrestricted control over the domain.

### Lab Scenario

The `helpdesk1` account represents a support technician.

The technician should be able to perform selected tasks, such as resetting passwords or unlocking accounts, for designated users.

The technician should not automatically be able to:

- Modify domain-wide security settings.
- Manage privileged administrative accounts.
- Change arbitrary security group memberships.
- Modify domain controller security settings.
- Grant themselves Domain Admin membership.

### Delegation Approach

Use Active Directory's delegation capabilities to grant narrowly scoped permissions to the relevant OU or objects.

For example, delegate password-reset and account-unlock permissions on a designated user OU to an appropriate support group.

Do not grant Domain Admin membership merely to make support tasks work.

### Testing

Perform both positive and negative tests.

| Test | Expected Result |
|---|---|
| Helpdesk resets an authorized user's password | Allowed |
| Helpdesk unlocks an authorized user's account | Allowed if configured |
| Helpdesk modifies an object outside the delegated scope | Denied |
| Helpdesk attempts to change protected domain-wide settings | Denied |
| Privileged administrative account performs an authorized domain task | Allowed |

The exact result depends on the permissions delegated and the security protections in place.

### Documentation

Record:

- Which account or group received delegated permissions.
- Which OU or objects are within scope.
- Which administrative tasks are permitted.
- Which actions are intentionally denied.
- How each behavior was tested.

This demonstrates an understanding of least privilege rather than simply assigning broad administrative roles.

---

## 11. DNS and Infrastructure Troubleshooting

Troubleshooting is an important part of systems administration.

A useful lab should demonstrate not only that services work, but also that common failures can be identified, investigated, and resolved.

### Scenario 1: Domain Authentication Failure

**Symptom:** A user cannot sign in to the domain.

Investigate:

1. Whether the account exists and is enabled.
2. Whether the password is correct.
3. Whether the account is locked or expired.
4. Whether the client has network connectivity.
5. Whether the client can resolve the domain controller.
6. Whether the system time is sufficiently synchronized for domain authentication.
7. Whether relevant authentication or directory-service events provide additional information.

Useful commands:

```powershell
whoami
ipconfig /all
nltest /dsgetdc:ad.comics.internal
```

`nltest` is available on supported Windows systems, including many Windows client and server installations.

### Scenario 2: DNS Failure

**Symptom:** A client cannot locate the domain controller or resolve the internal domain.

Investigate:

- The DNS servers configured on the client.
- Whether the internal DNS service is running.
- Whether the domain controller's DNS records exist.
- Whether the client can reach the DNS server.
- Whether DNS forwarding is required for external name resolution.

Useful commands:

```powershell
ipconfig /all
nslookup DC01.ad.comics.internal
nslookup -type=SRV _ldap._tcp.dc._msdcs.ad.comics.internal
```

The SRV record query helps verify that the client can locate Active Directory services through DNS.

### Scenario 3: File Access Failure

**Symptom:** A user can sign in but cannot open a departmental share.

Investigate:

1. Whether the user can reach the server.
2. Whether the share name is correct.
3. Whether the user's group memberships are correct.
4. Whether SMB share permissions permit access.
5. Whether NTFS permissions permit access.
6. Whether inherited permissions or explicit deny entries affect access.
7. Whether the user's logon session reflects recent group membership changes.

Useful commands:

```powershell
whoami /groups
Test-NetConnection DC01 -Port 445
```

A group membership change may require a new logon session before the user's access token reflects the change.

### Scenario 4: Group Policy Failure

**Symptom:** A workstation does not receive an expected setting.

Investigate:

- OU placement.
- GPO linkage.
- Security filtering.
- Policy scope.
- DNS and domain connectivity.
- Group Policy operational logs.

Useful commands:

```powershell
gpupdate /force
gpresult /r
```

### Scenario 5: Account Lockout

**Symptom:** A user cannot authenticate because the account is locked.

Investigate:

- Whether the account is locked.
- The account lockout policy.
- Relevant domain controller security events.
- Whether a device or application is repeatedly using an old password.
- Whether saved credentials or scheduled tasks could be contributing to the problem.

Unlock the account only after following the lab's intended support procedure.

### Troubleshooting Record Template

For each scenario, document:

| Field | Description |
|---|---|
| Symptom | What the user or administrator observed |
| Expected Behavior | What should have happened |
| Diagnostic Steps | Commands, logs, and settings examined |
| Root Cause | The underlying reason for the failure |
| Resolution | The corrective action |
| Verification | Evidence that the issue was resolved |

A good troubleshooting record explains how the root cause was identified, not just which setting was changed.

---

## 12. PowerShell Administration and Automation

PowerShell can reduce repetitive administrative work and make routine operations more consistent.

This lab should include a small collection of scripts with clear purposes, input validation, error handling, and documentation.

### Script 1: Bulk User Creation

**File:** `scripts/New-LabUsers.ps1`

Purpose:

- Read user information from a CSV file.
- Validate required fields.
- Check whether accounts already exist.
- Create user accounts in the intended OU.
- Assign approved group memberships.
- Report successes and failures.

Example CSV structure:

```csv
Username,FirstName,LastName,Department
retail1,Peter,Parker,Retail
retail2,Mary,Jane,Retail
creative1,Kamala,Khan,Publishing
creative2,Matt,Murdock,Publishing
```

The script should map each department to the correct OU and group configuration.

It should not blindly create accounts if the input is malformed or an account already exists.

**Security considerations:**

- Do not commit real employee data to a public repository.
- Do not hardcode plaintext passwords.
- Do not log temporary passwords.
- Use a secure process for initial passwords.
- Make the script safe to rerun where practical.
- Validate target OUs and group names before making changes.

### Script 2: User Account Audit

**File:** `scripts/Get-LabUserAudit.ps1`

Purpose:

- Enumerate users in selected OUs.
- Report account status.
- Identify disabled accounts.
- Report useful account attributes.
- Export results to CSV.

Potential report fields:

- Username.
- Display name.
- Department.
- Enabled status.
- Account expiration status.
- Last logon information, with its limitations clearly documented.

Active Directory's `LastLogonDate` is a replicated approximation derived from the `lastLogonTimestamp` attribute. It should not be treated as an exact, real-time last-logon record.

### Script 3: Group Membership Audit

**File:** `scripts/Export-LabGroupMembers.ps1`

Purpose:

- Enumerate selected security groups.
- Export their members.
- Help administrators review access assignments.
- Identify unexpected memberships.

The report should distinguish between direct membership and nested membership where relevant.

### Example: Find a User

```powershell
Get-ADUser -Identity retail1 -Properties Department,Enabled
```

### Example: List Enabled Users

```powershell
Get-ADUser -Filter 'Enabled -eq $true' -Properties Department |
    Select-Object SamAccountName, Name, Department, Enabled
```

### Example: View Group Members

```powershell
Get-ADGroupMember -Identity GG_Publishing
```

These commands require the Active Directory PowerShell module and appropriate permissions.

### Script Quality Expectations

Each script should include:

- A comment-based description.
- Documented parameters.
- Input validation.
- Appropriate error handling.
- Clear output.
- A least-privilege execution model.
- A sample input file where applicable.
- Instructions for testing safely in the lab.

---

## 13. Security Auditing and Event Logs

A systems administrator should be able to investigate important account and security changes.

Windows security auditing can record selected events on domain controllers and clients, depending on the audit configuration.

### Events to Investigate

| Activity | What to Investigate |
|---|---|
| User account creation | Which account was created and when |
| Account enablement or disablement | Who performed the action |
| Security group membership changes | Which membership changed |
| Failed logon attempts | Whether authentication is failing repeatedly |
| Account lockouts | Which account was locked and available supporting details |
| Group Policy changes | Whether policy configuration changed |
| File access | Whether relevant file-system auditing is configured and generating events |

### Configuration

1. Configure the relevant audit policies.
2. Apply the policies to the appropriate domain controllers or clients.
3. Perform controlled test actions.
4. Open Event Viewer.
5. Review the Security log and relevant operational logs.
6. Record the event IDs, timestamps, and details that support the investigation.

### Important Considerations

An event will not necessarily appear simply because an activity occurred.

The appropriate audit category or subcategory must be configured, the action must be covered by that audit setting, and the system must retain the resulting event.

For example, auditing file access requires appropriate audit policy and filesystem auditing configuration. Ordinary file access should not be assumed to generate a useful Security log event automatically.

### Evidence

Include sanitized screenshots or exported examples demonstrating selected events, such as:

- A user account being created.
- A group membership change.
- A failed logon.
- An account lockout, if configured and tested.

Do not publish sensitive account information, real credentials, or unnecessary security details.

---

## 14. Backup and Recovery

A reliable IT environment must be recoverable when configuration changes, data loss, or system failures occur.

The lab should demonstrate an understanding of both file recovery and Active Directory recovery.

### File Recovery

Test a basic file-recovery procedure:

1. Create a test document in a departmental share.
2. Back up the document using the selected backup method.
3. Modify or delete the document.
4. Restore the document.
5. Verify its contents and permissions.
6. Document the recovery steps and result.

The backup method may depend on the tools and storage available in the local environment.

### Active Directory Recovery

Active Directory recovery is different from ordinary file recovery.

A domain controller backup should account for the directory service and its supporting system state.

Research and document:

- What System State backup protects.
- How a domain controller backup differs from a file backup.
- The purpose of Directory Services Restore Mode (DSRM).
- The difference between restoring a deleted object and recovering a domain controller.
- Why domain controller recovery procedures must account for replication and directory consistency.

Do not experiment with destructive domain controller recovery procedures on the only copy of your lab without a verified recovery path.

### Recovery Documentation

Document:

- What data or configuration is protected.
- Where backups are stored.
- How often backups are taken.
- How recovery is initiated.
- What permissions or tools are required.
- How restoration is verified.
- Any limitations of the lab's backup design.

### Important Note

A virtual machine snapshot can be useful during lab experimentation, but it is not a substitute for a properly designed and tested backup strategy.

---

## 15. Operational Documentation

Good administrators document recurring tasks so that another technician can perform them consistently.

Create concise runbooks for the following tasks.

### User Administration

- Creating a user account.
- Changing a user's department.
- Disabling an account.
- Resetting a password.
- Unlocking an account.
- Reviewing group memberships.

### Computer Administration

- Joining a computer to the domain.
- Moving a computer object to the correct OU.
- Verifying DNS configuration.
- Confirming domain connectivity.
- Checking applied Group Policy.

### File Services

- Creating a departmental folder.
- Creating an SMB share.
- Configuring share permissions.
- Configuring NTFS permissions.
- Testing access with an authorized user.
- Troubleshooting denied access.

### Troubleshooting

- Diagnosing domain authentication failure.
- Diagnosing DNS failure.
- Investigating account lockouts.
- Diagnosing Group Policy issues.
- Investigating file-access problems.

### Recovery

- Restoring a deleted test file.
- Verifying a backup.
- Documenting the relevant Active Directory recovery procedures.

Each runbook should contain the purpose, prerequisites, procedure, verification steps, and relevant troubleshooting notes.

---

## 16. Implementation Plan

The lab can be implemented incrementally.

### Phase 1: Verify the Existing Domain

- Confirm the domain name.
- Verify the domain controller's hostname.
- Confirm that AD DS and DNS are operational.
- Verify that clients can resolve and locate the domain controller.
- Document the existing OU structure.

**Deliverable:** Verified domain environment and architecture documentation.

### Phase 2: Complete the OU and User Structure

- Verify the `Computers` OU.
- Verify the `Workstations` and `Servers` child OUs.
- Verify the location OUs under each.
- Verify the `Users` and department OU structure.
- Create the representative employee accounts.
- Place users and computer objects in the intended locations.

**Deliverable:** Documented OU hierarchy and representative user accounts.

### Phase 3: Implement Security Groups

- Create the departmental Global groups.
- Create the Domain Local resource groups.
- Add users to the appropriate Global groups.
- Nest the Global groups into the corresponding resource groups.
- Verify group memberships.

**Deliverable:** Documented group design and membership tests.

### Phase 4: Configure File Services

- Create the departmental folder structure.
- Configure SMB shares.
- Apply share permissions.
- Apply NTFS permissions.
- Test authorized and unauthorized access from both clients.

**Deliverable:** Working departmental file shares with documented access controls.

### Phase 5: Configure Group Policy

- Create a workstation baseline policy.
- Apply the policy to the appropriate workstation OU.
- Configure relevant account policies at the correct scope.
- Test policy application on the clients.
- Investigate and document at least one controlled policy issue.

**Deliverable:** Verified Group Policy configuration and troubleshooting evidence.

### Phase 6: Configure Delegated Administration

- Create or designate the support group.
- Delegate only the required user-management tasks.
- Test allowed actions.
- Test actions that should be denied.
- Document the scope of delegation.

**Deliverable:** Demonstrated least-privilege administration.

### Phase 7: Automate and Troubleshoot

- Implement the user provisioning script.
- Implement the user audit script.
- Implement the group membership export script.
- Document DNS, authentication, Group Policy, and file-access troubleshooting.
- Validate scripts using lab accounts.

**Deliverable:** Tested scripts and troubleshooting runbooks.

### Phase 8: Audit, Back Up, and Document

- Configure selected audit policies.
- Capture relevant security events.
- Back up a test file and verify restoration.
- Document Active Directory recovery concepts.
- Review the README and repository for accuracy.
- Remove sensitive information from all committed files.

**Deliverable:** A documented, reproducible, and demonstrable Active Directory lab.

---

## 17. Acceptance Criteria

The lab is considered complete when the following behaviors have been demonstrated and documented.

| ID | Requirement | Evidence |
|---|---|---|
| AC-01 | Domain controller and DNS are operational | DNS and domain discovery tests |
| AC-02 | Client machines are joined to the domain | Domain membership evidence |
| AC-03 | OU hierarchy matches the documented design | Active Directory OU screenshots or exported structure |
| AC-04 | Computer objects are placed under the correct workstation or server location OU | Computer object placement evidence |
| AC-05 | Representative employee accounts exist in the correct OUs | User object and OU evidence |
| AC-06 | Security groups represent departmental responsibilities | Group membership evidence |
| AC-07 | Departmental file shares are configured | Share configuration evidence |
| AC-08 | Authorized employees can access their required resources | Successful access tests |
| AC-09 | Unauthorized employees are denied restricted access | Failed access tests |
| AC-10 | Workstations receive the intended Group Policy settings | `gpresult` report or equivalent evidence |
| AC-11 | Delegated support tasks work within their intended scope | Positive and negative delegation tests |
| AC-12 | PowerShell scripts produce the expected results | Script output and sample reports |
| AC-13 | Selected security events can be investigated | Sanitized Event Viewer evidence |
| AC-14 | A test file can be restored from backup | Documented restoration test |

Do not mark an acceptance criterion as complete until the behavior has been tested.

Screenshots and command output should be sanitized to avoid exposing credentials, personal information, or unnecessary infrastructure details.

---

## 18. Repository Structure

The GitHub repository should make the project easy for another administrator or hiring manager to explore.

```text
active-directory-small-business-lab/
|
+-- README.md
|
+-- docs/
|   |
|   +-- architecture.md
|   +-- active-directory-design.md
|   +-- network-design.md
|   +-- group-policy.md
|   +-- file-server-permissions.md
|   +-- delegated-administration.md
|   +-- backup-and-recovery.md
|   |
|   +-- runbooks/
|   |   +-- user-onboarding.md
|   |   +-- user-offboarding.md
|   |   +-- password-reset.md
|   |   +-- domain-join.md
|   |   +-- file-share-access.md
|   |
|   +-- troubleshooting/
|       +-- dns.md
|       +-- authentication.md
|       +-- group-policy.md
|       +-- file-access.md
|
+-- scripts/
|   +-- New-LabUsers.ps1
|   +-- Get-LabUserAudit.ps1
|   +-- Export-LabGroupMembers.ps1
|
+-- samples/
|   +-- users.example.csv
|
+-- diagrams/
|   +-- infrastructure.mmd
|   +-- active-directory-structure.mmd
|
+-- evidence/
|   +-- domain-connectivity/
|   +-- group-policy/
|   +-- file-permissions/
|   +-- delegation/
|   +-- recovery/
|
+-- .gitignore
```

This is a proposed structure. Add files as the corresponding features are implemented rather than creating empty documentation for work that has not yet been completed.

### Documentation Standards

- Use Markdown for documentation.
- Use Mermaid for diagrams that should be version-controlled as text.
- Include commands and expected results where useful.
- Keep sample data fictional.
- Clearly label proposed configurations versus implemented configurations.
- Document the environment's limitations.
- Include evidence for completed acceptance criteria.

### GitHub Security

Before committing files:

- Remove passwords, secrets, tokens, and private keys.
- Avoid committing real user information.
- Review exported configuration and logs for sensitive information.
- Use `.gitignore` for temporary files, private configuration, and local artifacts.
- Never rely on a private repository as a substitute for removing secrets.

---

## 19. Future Enhancements

The following enhancements can expand the lab once the core requirements are complete.

### Dedicated Administrative Workstation

Add an `ADMIN01` workstation and install the appropriate Remote Server Administration Tools (RSAT).

Use it to demonstrate remote management of Active Directory and other supported Windows Server roles.

This would also provide an opportunity to explore privileged account separation and administrative workstation security.

### Secondary Domain Controller

Add a second domain controller to explore:

- Active Directory replication.
- DNS integration.
- Replication troubleshooting.
- Domain controller availability.
- Backup and recovery planning.

A second domain controller should be configured and tested rather than merely added to the architecture diagram.

### Dedicated File Server

Move file services from the domain controller to a separate member server.

This would demonstrate:

- Member server domain joining.
- SMB administration.
- File-server security.
- Independent server maintenance.
- Separation of infrastructure roles.

### Additional Security Improvements

Potential extensions include:

- Windows LAPS for local administrator password management.
- More detailed audit policies.
- Centralized event collection.
- Infrastructure monitoring.
- Additional PowerShell reporting.
- More advanced delegation scenarios.
- Fine-Grained Password Policies.
- Backup verification and recovery exercises.

Each enhancement should solve a specific operational problem and include a way to verify that it works.

---

## 20. Project Outcome

This project demonstrates how a small organization can use Windows Server and Active Directory to centralize identity management, organize computer and user objects, enforce access controls, and administer Windows workstations.

The lab emphasizes practical administration rather than configuration alone.

By completing the documented tests, scripts, and runbooks, the project provides evidence of the ability to:

- Manage Active Directory users, groups, and OUs.
- Troubleshoot domain connectivity and DNS.
- Configure Group Policy and verify its application.
- Implement departmental file access using security groups and permissions.
- Delegate routine support tasks according to least privilege.
- Automate administrative operations with PowerShell.
- Investigate security events.
- Document and test recovery procedures.

The environment is intentionally small and locally hosted. It is designed to demonstrate foundational Windows infrastructure skills while providing a clear path toward more advanced enterprise administration.

**The goal is not simply to have a functioning domain
