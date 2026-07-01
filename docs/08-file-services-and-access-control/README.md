# File Services and Access Control

This chapter documents Windows file services, home folders, DATA share permissions, mapped drives, access testing, and FSRM quota controls.


## Technical Context

DC2 is configured as a file server. The lab creates home folders, a shared DATA folder, a script share, mapped drives, and an FSRM quota.

The lab demonstrates layered file access: AD groups define roles, share and NTFS permissions enforce access, mapped drives improve usability, and FSRM controls storage consumption.

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

---

## Detailed Walkthrough

### Step 01 - Install the File Server role

DC2 is prepared to host file shares for domain users.

> A file server centralizes storage and lets administrators enforce permissions, quotas, and file controls from one managed server.

![File Server Role Selection](../../images/09-file-server/01-file-server-role-selection.png)

<p><sub><strong>Screenshot 104 - File Server Role Selection:</strong> File Server role selected on DC2.</sub></p>

---

### Step 02 - Create the home folder root

The home folder root is created and shared for per-user mapped storage.

> Home folders give users personal network storage while keeping permissions, backups, quotas, and administration centralized.

![Home Folder Root Created](../../images/09-file-server/02-home-folder-root-created.png)

<p><sub><strong>Screenshot 105 - Home Folder Root Created:</strong> Home folder root created on DC2.</sub></p>

---

### Step 03 - Map home folders through AD

AD user properties are configured with a home folder path. Windows creates the user-specific folder when the mapping applies.

> AD-based home-folder mapping keeps storage tied to the user account and avoids manual mapping on each workstation.

![AD Home Folder Mapping](../../images/09-file-server/05-ad-home-folder-mapping.png)

<p><sub><strong>Screenshot 108 - AD Home Folder Mapping:</strong> Home folder mapping configured in AD user properties.</sub></p>

---

### Step 04 - Validate mapped home drives

The Windows 10 client shows the mapped home drive, confirming the user folder mapping worked.

> Client validation checks AD settings, share access, connectivity, and permissions together.

![Mapped Home Drive Visible](../../images/09-file-server/07-mapped-home-drive-visible.png)

<p><sub><strong>Screenshot 110 - Mapped Home Drive Visible:</strong> Mapped home drive visible on the client.</sub></p>

---

### Step 05 - Configure DATA share permissions

The DATA share is configured with group-based permissions. `Sys_Admins` receive Modify permission, while Sales users receive Read and Execute.

Group-based access is easier to review, remove, and adjust when users move between roles.

> Share and NTFS permissions work together. Effective access is the most restrictive result between both layers.

![SysAdmins Modify Permission](../../images/09-file-server/11-sysadmins-modify-permission.png)

<p><sub><strong>Screenshot 114 - SysAdmins Modify Permission:</strong> Sys_Admins Modify permission on the DATA share.</sub></p>

`Sales_group` receives **Read and Execute**, **List folder contents**, and **Read**, allowing Sales users to view content without modifying or deleting files.

![Sales Read and Execute Permission](../../images/09-file-server/12-sales-read-execute-permission.png)

<p><sub><strong>Screenshot 115 - Sales Read and Execute Permission:</strong> Sales_group granted Read and Execute access to the DATA folder.</sub></p>

---

### Step 06 - Validate restricted Sales permissions

The Sales user can access the share but cannot delete files, proving that the permission boundary works.

> Access-control testing should include allowed and blocked actions. A successful denial proves least privilege is enforced.

![Sales Delete Denied Test](../../images/09-file-server/14-sales-delete-denied-test.png)

<p><sub><strong>Screenshot 117 - Sales Delete Denied Test:</strong> Sales user denied delete operation.</sub></p>

---

### Step 07 - Create a script-based drive mapping

A shared `Script` folder on DC2 stores the client batch file. In this isolated lab, `Everyone` receives Modify access for testing.

> Broad `Everyone` Modify access is lab-only. In production, users should normally have Read and Execute while only administrators can modify scripts.

![Script Folder Permissions](../../images/09-file-server/15-script-share-everyone-modify.png)

<p><sub><strong>Screenshot 118 - Script Folder Permissions:</strong> Lab permissions on the Script folder allowing Everyone to modify its contents.</sub></p>

The folder is shared as `\\SAMDC2\Script`, giving workstations a stable UNC path.

![Script Share Network Path](../../images/09-file-server/16-script-share-network-path.png)

<p><sub><strong>Screenshot 119 - Script Share Network Path:</strong> Script folder shared from DC2 through the `\\SAMDC2\Script` network path.</sub></p>

`Script.bat` contains `net use X: \\Samdc2\data`, which maps the DATA share to drive `X:` for users.

> `net use` is the classic Windows share-mapping command. Group Policy Preferences are easier to maintain at scale, but the batch file shows the underlying method.

![Net Use Batch Script](../../images/09-file-server/17-net-use-batch-script.png)

<p><sub><strong>Screenshot 120 - Net Use Batch Script:</strong> Batch script using `net use` to map the DATA share.</sub></p>

After the script runs, `SAMWINPC1` shows `data (\\Samdc2) (X:)`, confirming name resolution, share access, and drive mapping.

![Script Mapped Drive Validation](../../images/09-file-server/18-script-mapped-drive-validation.png)

<p><sub><strong>Screenshot 121 - Script Mapped Drive Validation:</strong> DATA share mapped as drive X on SAMWINPC1 after running the batch script.</sub></p>

---

### Step 08 - Configure a 5 GB hard quota

File Server Resource Manager enforces a hard quota on home folders. This is a file-server control, not an Active Directory quota.

> FSRM means File Server Resource Manager. A hard quota blocks users from exceeding the limit and protects shared storage from uncontrolled growth.

![Hard Quota 5GB](../../images/09-file-server/21-hard-quota-5gb.png)

<p><sub><strong>Screenshot 124 - Hard Quota 5GB:</strong> Hard quota configured for a 5 GB limit.</sub></p>

---

## Validation and Summary


Validation confirms file-server role installation, folder and share creation, NTFS permissions, AD home-folder mapping, mapped drive visibility, user access tests, script-based drive mapping, and hard quota configuration.


This chapter provides the shared storage layer and shows how AD groups, NTFS permissions, share access, mapped drives, and FSRM quotas work together.

---

## Project Chapters

| # | Chapter |
|---|---------|
| 0 | [Project Overview](../../README.md) |
| 1 | [Topology and Lab Environment](../01-topology-and-lab-environment/README.md) |
| 2 | [Active Directory Domain Services](../02-active-directory-domain-services/README.md) |
| 3 | [NAT and Routing with RRAS](../03-nat-and-rras-routing/README.md) |
| 4 | [DHCP Services and Failover](../04-dhcp-services-and-failover/README.md) |
| 5 | [Remote Administration](../05-remote-administration/README.md) |
| 6 | [DNS Services and Name Resolution](../06-dns-services-and-name-resolution/README.md) |
| 7 | [Roaming and Mandatory Profiles](../07-roaming-and-mandatory-profiles/README.md) |
| 8 | [File Services and Access Control](../08-file-services-and-access-control/README.md) |
| 9 | [Group Policy Hardening and Software Deployment](../09-group-policy-hardening-and-software-deployment/README.md) |
| 10 | [Password Policy and Account Security](../10-password-policy-and-account-security/README.md) |
| 11 | [Final Summary](../11-final-summary/README.md) |
