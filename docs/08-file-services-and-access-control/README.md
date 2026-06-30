# File Services and Access Control

This chapter covers File Services and Access Control in the Windows Server infrastructure lab. It explains what was configured, why the configuration matters, and which evidence validates the result.

---

## Purpose

Deploy file services, home folders, DATA share permissions, mapped drives, and FSRM quota controls for domain users and groups.

## Technical Context

DC2 is configured as a file server. The lab creates home folders, a shared DATA folder, a script share, mapped drives, and an FSRM quota.

The purpose is to demonstrate how enterprise file access is controlled through layers: share permissions, NTFS permissions, AD groups, mapped drives, and quota limits. AD defines users and groups, NTFS and share permissions enforce access, and FSRM controls storage consumption.

**Implemented controls:**

- Installed the file server role on DC2.
- Created home folders and mapped them through AD.
- Configured group-based DATA share permissions.
- Validated allowed and denied user actions.
- Built a script-based drive mapping with `net use`.
- Enforced a 5 GB hard quota with FSRM.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| NTFS permission | File-system permission applied directly to folders and files on a Windows volume. |
| Share permission | Network access permission applied when a folder is accessed over SMB. |
| Home folder | A per-user storage location mapped automatically through Active Directory. |
| Mapped drive | A drive letter that points to a network share. |
| FSRM quota | File Server Resource Manager control that limits storage consumption. |

## Steps Covered

| Step | Description |
|------|-------------|
| Install the File Server role | DC2 is prepared to host file shares for domain users. |
| Create the home folder root | The home folder root is created and shared so each user can receive a personal mapped folder. |
| Map home folders through AD | The AD user properties are configured with a home folder path. |
| Validate mapped home drives | The Windows 10 client shows the mapped home drive, confirming the user folder mapping worked. |
| Configure DATA share permissions | The DATA share is configured with group based permissions. |
| Validate restricted Sales permissions | The Sales user test confirms that the user can access the share but cannot delete files. |
| Create a script-based drive mapping | A shared Script folder is prepared on DC2 to store the batch file used by domain clients. |
| Configure a 5 GB hard quota | File Server Resource Manager is used to enforce a hard quota on home folders. |

---

## Detailed Walkthrough

### Step 01 - Install the File Server role

DC2 is prepared to host file shares for domain users.

> A file server centralizes storage so users do not depend only on local workstation disks. It also allows administrators to enforce permissions, quotas, and file-type controls from one managed server.

![File Server Role Selection](../../images/09-file-server/01-file-server-role-selection.png)

<p><sub><strong>Screenshot 104 - File Server Role Selection:</strong> File Server role selected on DC2.</sub></p>

---

### Step 02 - Create the home folder root

The home folder root is created and shared so each user can receive a personal mapped folder.

> Home folders give users a dedicated network location for personal files. Keeping them under one root folder makes permissions, backups, quotas, and administration easier to manage.

![Home Folder Root Created](../../images/09-file-server/02-home-folder-root-created.png)

<p><sub><strong>Screenshot 105 - Home Folder Root Created:</strong> Home folder root created on DC2.</sub></p>

---

### Step 03 - Map home folders through AD

The AD user properties are configured with a home folder path. Windows creates the user-specific folder when the mapping is applied.

> Mapping home folders through Active Directory keeps storage assignment tied to the user account. When the user signs in, Windows can automatically present the same home drive without manual mapping on each workstation.

![AD Home Folder Mapping](../../images/09-file-server/05-ad-home-folder-mapping.png)

<p><sub><strong>Screenshot 108 - AD Home Folder Mapping:</strong> Home folder mapping configured in AD user properties.</sub></p>

---

### Step 04 - Validate mapped home drives

The Windows 10 client shows the mapped home drive, confirming the user folder mapping worked.

> Validation from the client proves that the user can actually see and use the mapped drive. This checks AD settings, share access, network connectivity, and permissions together.

![Mapped Home Drive Visible](../../images/09-file-server/07-mapped-home-drive-visible.png)

<p><sub><strong>Screenshot 110 - Mapped Home Drive Visible:</strong> Mapped home drive visible on the client.</sub></p>

---

### Step 05 - Configure DATA share permissions

The DATA share is configured with group-based permissions. `Sys_Admins` receive Modify permission, while Sales users receive Read and Execute.

The security goal is to avoid assigning access directly to individual users. Group-based access is easier to review, easier to remove, and safer when users move between roles.

> Share permissions and NTFS permissions work together. The effective access is the most restrictive result between them, so both layers must match the intended access model.

![SysAdmins Modify Permission](../../images/09-file-server/11-sysadmins-modify-permission.png)

<p><sub><strong>Screenshot 114 - SysAdmins Modify Permission:</strong> Sys_Admins Modify permission on the DATA share.</sub></p>

The `Sales_group` receives **Read and Execute**, **List folder contents**, and **Read** permissions. These permissions allow Sales users to open the shared folder and read its contents without granting the ability to modify or delete files.

![Sales Read and Execute Permission](../../images/09-file-server/12-sales-read-execute-permission.png)

<p><sub><strong>Screenshot 115 - Sales Read and Execute Permission:</strong> Sales_group granted Read and Execute access to the DATA folder.</sub></p>

---

### Step 06 - Validate restricted Sales permissions

The Sales user test confirms that the user can access the share but cannot delete files. This denied action is important validation: it proves that the permission boundary works, not only that access is possible.

> Access-control testing should include both allowed and blocked actions. A successful denial proves that least privilege is enforced and that the Sales role cannot perform administrative file operations.

![Sales Delete Denied Test](../../images/09-file-server/14-sales-delete-denied-test.png)

<p><sub><strong>Screenshot 117 - Sales Delete Denied Test:</strong> Sales user denied delete operation.</sub></p>

---

### Step 07 - Create a script-based drive mapping

A shared `Script` folder is prepared on DC2 to store the batch file used by domain clients. In this isolated lab, `Everyone` receives Modify access so the script can be reached and updated during testing.

> Broad `Everyone` Modify permission is convenient for a training environment but is not appropriate for production. A real deployment should normally grant users only Read and Execute access while limiting script modification to an administrative group, because a writable logon or mapping script could be altered to execute unwanted commands.

![Script Folder Permissions](../../images/09-file-server/15-script-share-everyone-modify.png)

<p><sub><strong>Screenshot 118 - Script Folder Permissions:</strong> Lab permissions on the Script folder allowing Everyone to modify its contents.</sub></p>

The folder is shared as `\\SAMDC2\Script`, giving workstations a UNC path that does not depend on the local drive layout of the server.

![Script Share Network Path](../../images/09-file-server/16-script-share-network-path.png)

<p><sub><strong>Screenshot 119 - Script Share Network Path:</strong> Script folder shared from DC2 through the `\\SAMDC2\Script` network path.</sub></p>

The `Script.bat` file contains `net use X: \\Samdc2\data`. When the batch file runs, Windows connects the DATA share and assigns it the drive letter `X:`. This demonstrates a simple, repeatable method for providing users with a familiar drive letter for a network resource.

> `net use` is the classic Windows command for connecting a network share. Group Policy Preferences are generally easier to target and maintain in a larger production environment, but the batch-file method clearly demonstrates the underlying mapping command.

![Net Use Batch Script](../../images/09-file-server/17-net-use-batch-script.png)

<p><sub><strong>Screenshot 120 - Net Use Batch Script:</strong> Batch script using `net use` to map the DATA share.</sub></p>

After the script runs on `SAMWINPC1`, File Explorer shows `data (\\Samdc2) (X:)` under Network locations. This confirms that the client resolved DC2, reached the DATA share, and created the requested drive mapping.

![Script Mapped Drive Validation](../../images/09-file-server/18-script-mapped-drive-validation.png)

<p><sub><strong>Screenshot 121 - Script Mapped Drive Validation:</strong> DATA share mapped as drive X on SAMWINPC1 after running the batch script.</sub></p>

---

### Step 08 - Configure a 5 GB hard quota

File Server Resource Manager is used to enforce a hard quota on home folders. This is an FSRM/file-server control, not an Active Directory quota.

> FSRM means File Server Resource Manager. A hard quota blocks users from exceeding the limit, which protects shared storage from uncontrolled growth and helps enforce storage policy.

![Hard Quota 5GB](../../images/09-file-server/21-hard-quota-5gb.png)

<p><sub><strong>Screenshot 124 - Hard Quota 5GB:</strong> Hard quota configured for a 5 GB limit.</sub></p>

---

## Validation

Validation is based on file-server role installation, folder and share creation, NTFS permissions, AD home-folder mapping, mapped drive visibility, user access tests, a batch-based drive mapping, and the hard quota configuration.

## Chapter Summary

This chapter provides the shared storage layer for the lab. It demonstrates the relationship between AD groups, NTFS permissions, share access, home folders, mapped drives, and FSRM quota enforcement.

---

## Project Chapters

| Chapter | Description |
|---------|-------------|
| [Project Overview](../../README.md) | Main project overview, topology, scope, and outcomes |
| [Network Topology and Lab Planning](../01-network-topology-and-lab-planning/README.md) | Define the lab topology, domain name, server roles, addressing plan, operating-system baseline, and virtualization inventory before infrastructure services are installed. |
| [Active Directory Domain Services](../02-active-directory-domain-services/README.md) | Build the identity foundation by promoting DC1, adding DC2, creating administrative structures, automating account creation, and validating directory replication. |
| [NAT and Routing with RRAS](../03-nat-and-rras-routing/README.md) | Configure SAMNAT as the lab routing server so internal domain systems can reach external networks through a controlled NAT path. |
| [DHCP Services and Failover](../04-dhcp-services-and-failover/README.md) | Deploy DHCP services so Windows clients can receive consistent IP addressing, gateway, DNS, and lease settings automatically. |
| [Remote Administration](../05-remote-administration/README.md) | Enable controlled Remote Desktop administration for approved administrators and document a lab-only NAT forwarding test. |
| [DNS Services and Name Resolution](../06-dns-services-and-name-resolution/README.md) | Configure internal and external DNS behavior, including resolver settings, forwarders, controlled zones, conditional forwarding, stub zones, secondary zones, host records, and round robin records. |
| [Roaming and Mandatory Profiles](../07-roaming-and-mandatory-profiles/README.md) | Configure roaming profile storage and convert a profile to mandatory mode so user profile behavior can be controlled across domain workstations. |
| [File Services and Access Control](../08-file-services-and-access-control/README.md) | Deploy file services, home folders, DATA share permissions, mapped drives, and FSRM quota controls for domain users and groups. |
| [Group Policy Hardening and Software Deployment](../09-group-policy-hardening-and-software-deployment/README.md) | Apply domain-based workstation controls through Group Policy, including user restrictions, removable-storage controls, administrator exceptions, local administrator targeting, and MSI software deployment. |
| [Password Policy and Account Security](../10-password-policy-and-account-security/README.md) | Configure a domain-linked password baseline and explain how password policy supports account security in the lab. |
| [Testing, Results, and Recommendations](../11-testing-results-and-recommendations/README.md) | Final validation, production notes, limitations, skills, and recommendations |

