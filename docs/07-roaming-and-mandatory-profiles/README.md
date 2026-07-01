# Roaming and Mandatory Profiles

This chapter covers Roaming and Mandatory Profiles in the Windows Server infrastructure lab. It explains what was configured, why the configuration matters, and which evidence validates the result.


## Technical Context

Roaming profiles allow user profile data to follow the user across workstations. An AD user account represents identity and authentication, while a Windows user profile contains the user environment, settings, and profile data loaded during logon.

The goal of this section is to show centralized user-environment management. Roaming profiles support mobility between computers, while mandatory profiles enforce a consistent, non-persistent user environment.

**Implemented controls:**

- Created a centralized profile storage folder.
- Shared the profile location for domain access.
- Assigned the roaming profile path through AD user properties.
- Validated server-side profile folder creation after logon.
- Converted a roaming profile into a mandatory profile.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| Roaming profile | A user profile stored on a server so settings can follow the user between workstations. |
| Profile path | The AD user-account setting that points Windows to the server-side profile location. |
| `NTUSER.DAT` | The profile registry hive that stores user-specific Windows settings. |
| `NTUSER.MAN` | The mandatory-profile version of the hive, causing profile changes to be discarded at sign-out. |

---

## Detailed Walkthrough

### Step 01 - Create the profile storage folder

A profile folder is created on DC1 to store roaming profile data centrally.

> Roaming profiles need a central network location where profile data can be saved and loaded. Without a shared storage location, the user profile remains tied to one workstation.

![Profile Folder Created](../../images/08-roaming-profiles/01-profile-folder-created.png)

<p><sub><strong>Screenshot 092 - Profile Folder Created:</strong> Roaming profile root folder created on DC1.</sub></p>

---

### Step 02 - Configure profile share permissions

The folder is shared so users can access their roaming profile location through the network.

> The share makes the folder reachable over the network, while permissions control who can read or write profile data. Profile shares must be protected because they can contain user settings and personal application data.

![Advanced Sharing for Profile Folder](../../images/08-roaming-profiles/04-advanced-sharing-for-profile-folder.png)

<p><sub><strong>Screenshot 095 - Advanced Sharing for Profile Folder:</strong> Advanced sharing configuration for the profile folder.</sub></p>

---

### Step 03 - Assign the profile path in AD

The user object receives a profile path that points to the roaming profile share.

> Active Directory stores the profile path on the user object so Windows knows where to load and save that user's profile during logon and logoff. This connects identity management with the user's working environment.

![AD Profile Path](../../images/08-roaming-profiles/05-ad-profile-path.png)

<p><sub><strong>Screenshot 096 - AD Profile Path:</strong> Active Directory profile path configured for a user.</sub></p>

---

### Step 04 - Confirm profile folder creation

After the user signs in, the server-side profile folder is created automatically.

> This proves the profile configuration works from the user's session, not only from the administrator's settings. It confirms that Windows can create and use the central profile location.

![User Profile Folder Created](../../images/08-roaming-profiles/06-user-profile-folder-created.png)

<p><sub><strong>Screenshot 097 - User Profile Folder Created:</strong> User profile folders created after logon.</sub></p>

---

### Step 05 - Convert the profile to mandatory

After the roaming profile has been created and the user is logged off, the administrator opens the server-side profile folder and renames `NTUSER.DAT` to `NTUSER.MAN`. Windows then loads the profile as mandatory during the next sign-in.

> `NTUSER.DAT` contains user-specific registry settings such as desktop and application preferences. Renaming it to `NTUSER.MAN` makes those settings read-only from the user's perspective: the user can work normally during the session, but profile changes are discarded at sign-out. Mandatory profiles are useful for shared workstations, training rooms, and other environments that require the same controlled desktop at every logon.

![NTUSER.MAN Mandatory Profile](../../images/08-roaming-profiles/11-ntuser-man-mandatory-profile.png)

<p><sub><strong>Screenshot 102 - NTUSER.MAN Mandatory Profile:</strong> `NTUSER.MAN` conversion for a mandatory profile.</sub></p>

The User Profiles control panel reports the account profile as `Mandatory`, confirming that Windows recognizes the conversion rather than treating it as a standard local or roaming profile.

![Mandatory Profile Validation](../../images/08-roaming-profiles/12-mandatory-profile-visible.png)

<p><sub><strong>Screenshot 103 - Mandatory Profile Validation:</strong> The User Profiles window on PC1 reporting `SAMUELDOMAIN\user10` as a Mandatory profile.</sub></p>

---

## Validation and Summary


Validation is based on profile folder creation, share and NTFS permissions, AD profile path assignment, client-side profile visibility, owner and permission updates, NTUSER.MAN conversion, and the mandatory profile status screen.


This chapter demonstrates domain profile management. Roaming profiles provide portable user settings, while mandatory profiles enforce a controlled profile state that discards user changes at sign-out.

---

## Project Chapters

| # | Chapter | Description |
|---|---------|-------------|
| 0 | [Project Overview](../../README.md) | Main project overview, objectives, tools, and skills |
| 1 | [Topology and Lab Environment](../01-topology-and-lab-environment/README.md) | Lab topology, addressing, server roles, operating-system baseline, and virtualization inventory |
| 2 | [Active Directory Domain Services](../02-active-directory-domain-services/README.md) | Domain-controller deployment, administrative structures, scripted account creation, FSMO work, and AD replication validation |
| 3 | [NAT and Routing with RRAS](../03-nat-and-rras-routing/README.md) | SAMNAT routing, RRAS NAT configuration, and outbound connectivity validation |
| 4 | [DHCP Services and Failover](../04-dhcp-services-and-failover/README.md) | DHCP scope, exclusions, options, client lease validation, and DHCP failover |
| 5 | [Remote Administration](../05-remote-administration/README.md) | RDP administration, administrator group access, and lab-only NAT forwarding validation |
| 6 | [DNS Services and Name Resolution](../06-dns-services-and-name-resolution/README.md) | Forwarders, controlled zones, conditional forwarding, stub zones, secondary zones, host records, and round robin |
| 7 | [Roaming and Mandatory Profiles](../07-roaming-and-mandatory-profiles/README.md) | Roaming profile storage, profile paths, server-side profile folders, and mandatory profile conversion |
| 8 | [File Services and Access Control](../08-file-services-and-access-control/README.md) | File services, home folders, DATA permissions, mapped drives, and FSRM quota controls |
| 9 | [Group Policy Hardening and Software Deployment](../09-group-policy-hardening-and-software-deployment/README.md) | User restrictions, removable-storage controls, administrator exceptions, local administrator targeting, and MSI deployment |
| 10 | [Password Policy and Account Security](../10-password-policy-and-account-security/README.md) | Domain password policy baseline and account-security explanation |
| 11 | [Final Summary](../11-final-summary/README.md) | Validation summary, production recommendations, skills, and project closure |
