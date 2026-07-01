# Roaming and Mandatory Profiles

This chapter documents roaming profile storage, AD profile paths, client profile behavior, and conversion of a profile to mandatory mode.


## Technical Context

Roaming profiles allow user settings and profile data to follow the user across workstations. AD stores identity; the Windows profile stores the user environment loaded at logon.

Roaming profiles support mobility, while mandatory profiles enforce a consistent, non-persistent environment.

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

> Roaming profiles need central storage; otherwise the user profile remains tied to one workstation.

![Profile Folder Created](../../images/08-roaming-profiles/01-profile-folder-created.png)

<p><sub><strong>Screenshot 092 - Profile Folder Created:</strong> Roaming profile root folder created on DC1.</sub></p>

---

### Step 02 - Configure profile share permissions

The folder is shared so users can access their roaming profile location through the network.

> The share exposes the profile path, while permissions protect profile data that may contain user settings and application data.

![Advanced Sharing for Profile Folder](../../images/08-roaming-profiles/04-advanced-sharing-for-profile-folder.png)

<p><sub><strong>Screenshot 095 - Advanced Sharing for Profile Folder:</strong> Advanced sharing configuration for the profile folder.</sub></p>

---

### Step 03 - Assign the profile path in AD

The user object receives a profile path that points to the roaming profile share.

> AD stores the profile path on the user object so Windows knows where to load and save the profile during logon and logoff.

![AD Profile Path](../../images/08-roaming-profiles/05-ad-profile-path.png)

<p><sub><strong>Screenshot 096 - AD Profile Path:</strong> Active Directory profile path configured for a user.</sub></p>

---

### Step 04 - Confirm profile folder creation

After the user signs in, the server-side profile folder is created automatically.

> This proves the profile path works from the user's session, not only from the administrator's settings.

![User Profile Folder Created](../../images/08-roaming-profiles/06-user-profile-folder-created.png)

<p><sub><strong>Screenshot 097 - User Profile Folder Created:</strong> User profile folders created after logon.</sub></p>

---

### Step 05 - Convert the profile to mandatory

After the roaming profile is created and the user logs off, `NTUSER.DAT` is renamed to `NTUSER.MAN`. Windows then loads the profile as mandatory at the next sign-in.

> `NTUSER.DAT` stores user registry settings. Renaming it to `NTUSER.MAN` makes the profile mandatory: users can work normally, but changes are discarded at sign-out.

![NTUSER.MAN Mandatory Profile](../../images/08-roaming-profiles/11-ntuser-man-mandatory-profile.png)

<p><sub><strong>Screenshot 102 - NTUSER.MAN Mandatory Profile:</strong> `NTUSER.MAN` conversion for a mandatory profile.</sub></p>

The User Profiles panel reports the account profile as `Mandatory`, confirming that Windows recognizes the conversion.

![Mandatory Profile Validation](../../images/08-roaming-profiles/12-mandatory-profile-visible.png)

<p><sub><strong>Screenshot 103 - Mandatory Profile Validation:</strong> The User Profiles window on PC1 reporting `SAMUELDOMAIN\user10` as a Mandatory profile.</sub></p>

---

## Validation and Summary


Validation confirms profile folders, share and NTFS permissions, AD profile paths, client visibility, owner updates, `NTUSER.MAN` conversion, and mandatory profile status.


This chapter demonstrates roaming profile storage and mandatory profile enforcement for a controlled user environment.

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
