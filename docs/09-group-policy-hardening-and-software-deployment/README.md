# Group Policy Hardening and Software Deployment

This chapter documents Group Policy hardening and software deployment for the Windows client environment.


## Technical Context

Group Policy restricts standard users, handles `Sys_Admins` exceptions, targets local administrator membership, and deploys software.

The hardening focus is to reduce a standard user's ability to change workstation settings, launch administrative tools, or move data through removable media.

**Implemented controls:**

- Created client-hardening GPOs.
- Restricted Command Prompt and Control Panel access for standard users.
- Blocked removable storage access.
- Configured a Restricted Groups policy intended to add `Sys_Admins` to local Administrators on managed workstations.
- Deployed Notepad++ through GPO software installation.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| GPO | Group Policy Object, a container for user or computer settings applied through Active Directory. |
| OU link | The connection between a GPO and an Organizational Unit that defines where the policy applies. |
| Restricted Groups | A policy setting that controls membership of local groups such as Administrators. |
| Computer Configuration | GPO settings that apply to computer accounts, usually at startup or background refresh. |
| MSI deployment | Software installation through Group Policy using a network-accessible Windows Installer package. |

---

## Detailed Walkthrough

### Step 01 - Open Group Policy Management

Group Policy Management provides centralized control for domain-linked policies.

> A GPO is a collection of user or computer settings. Group Policy Management is where those policies are created, linked, and reviewed.

![Group Policy Management Opened](../../images/10-group-policy-hardening/01-group-policy-management-opened.png)

<p><sub><strong>Screenshot 127 - Group Policy Management Opened:</strong> Group Policy Management opened.</sub></p>

---

### Step 02 - Create a client-hardening GPO

A new GPO is created for Command Prompt, Control Panel, and removable-storage restrictions.

> A dedicated hardening GPO keeps workstation restrictions separate from unrelated domain policy and easier to troubleshoot.

![Create New Hardening GPO](../../images/10-group-policy-hardening/03-create-new-hardening-gpo.png)

<p><sub><strong>Screenshot 129 - Create New Hardening GPO:</strong> Creating a new hardening GPO.</sub></p>

---

### Step 03 - Restrict Command Prompt access

Inside **User Configuration > Policies > Administrative Templates > System**, **Prevent access to the command prompt** is enabled in `BLOCK CMD & CP`. This blocks targeted standard users from launching `cmd.exe`.

> Blocking Command Prompt is an endpoint restriction, not full application control. PowerShell and other interpreters should be controlled separately when stronger execution control is required.

![Command Prompt Access Blocked](../../images/10-group-policy-hardening/05-command-prompt-deny-policy.png)

<p><sub><strong>Screenshot 131 - Command Prompt Access Blocked:</strong> Prevent access to the command prompt enabled in the BLOCK CMD & CP GPO.</sub></p>

---

### Step 04 - Restrict access to Control Panel and PC settings

Inside **User Configuration > Policies > Administrative Templates > Control Panel**, **Prohibit access to Control Panel and PC settings** is enabled. This blocks both `Control.exe` and `SystemSettings.exe` for targeted users.

> This user-side policy helps prevent standard users from changing settings that could weaken the workstation baseline or interfere with support.

![Control Panel and PC Settings Restricted](../../images/10-group-policy-hardening/04-hardening-gpo-editor-settings.png)

<p><sub><strong>Screenshot 130 - Control Panel and PC Settings Restricted:</strong> Prohibit access to Control Panel and PC settings enabled in the BLOCK CMD & CP GPO.</sub></p>

---

### Step 05 - Validate standard-user restriction

The standard user is blocked from accessing Control Panel, confirming that the hardening policy applies.

> Validation confirms that the restriction reaches the intended user and blocks Control Panel from the endpoint side.

![Standard User Control Panel Blocked](../../images/10-group-policy-hardening/12-standard-user-control-panel-blocked.png)

<p><sub><strong>Screenshot 138 - Standard User Control Panel Blocked:</strong> Standard user blocked from Control Panel.</sub></p>

---

### Step 06 - Configure removable storage restrictions

The `BLOCK Disk On Key` GPO is linked at the domain level for removable-storage control.

![Block Disk On Key GPO Link](../../images/10-group-policy-hardening/09-gpo-linking-overview.png)

<p><sub><strong>Screenshot 135 - Block Disk On Key GPO Link:</strong> BLOCK Disk On Key GPO linked beneath the SamuelDomain.com domain.</sub></p>

The policy denies removable media access to reduce data exfiltration, unauthorized copying, and untrusted USB files.

> Blocking removable media through policy is a common endpoint-hardening control for standard users.

![Removable Storage Deny Policy](../../images/10-group-policy-hardening/08-removable-storage-deny-policy.png)

<p><sub><strong>Screenshot 134 - Removable Storage Deny Policy:</strong> Removable storage deny policy.</sub></p>

---

### Step 07 - Add Sys_Admins to local Administrators

The `Add to Local Admins` GPO is linked under `Sys_Admins` before the Restricted Groups setting is reviewed.

![Add to Local Admins GPO](../../images/10-group-policy-hardening/10-add-sysadmins-to-local-admins-gpo.png)

<p><sub><strong>Screenshot 136 - Add to Local Admins GPO:</strong> Add to Local Admins GPO linked beneath the Sys_Admins OU.</sub></p>

Restricted Groups are configured to place `Sys_Admins` into local Administrators on managed workstations. Because this is a **Computer Configuration** setting, the GPO must apply to target computer accounts.

> Restricted Groups can enforce local group membership from the domain. If linked only to an OU with user accounts, the computer-side setting will not reach workstations. Validate with `gpresult /scope computer /r` and `net localgroup administrators`.

> Security note: Local administrator membership should be limited, reviewed, monitored, and tied to least-privilege procedures.

![Restricted Groups Membership](../../images/10-group-policy-hardening/11-restricted-groups-membership.png)

<p><sub><strong>Screenshot 137 - Restricted Groups Membership:</strong> Restricted Groups membership configuration.</sub></p>

---

### Step 08 - Apply Sys_Admins workstation rules

The `Allow CMD & CP` GPO gives approved administrators Command Prompt and Control Panel access for support work.

> This is an exception policy: standard users are restricted, while approved admins keep the tools needed for diagnostics and fixes.

![SysAdmins GPO Link](../../images/10-group-policy-hardening/02-gpo-list-overview.png)

<p><sub><strong>Screenshot 128 - SysAdmins GPO Link:</strong> Allow CMD & CP GPO linked beneath the Sys_Admins OU.</sub></p>

Inside this policy, **Prevent access to the command prompt** is set to **Disabled**, so targeted `Sys_Admins` users are not blocked from `cmd.exe`.

![SysAdmins Command Prompt Rule](../../images/10-group-policy-hardening/06-sysadmins-command-prompt-exception.png)

<p><sub><strong>Screenshot 132 - SysAdmins Command Prompt Rule:</strong> Command Prompt restriction disabled in the Sys_Admins policy.</sub></p>

The Control Panel restriction is also disabled, allowing targeted administrators to open required management interfaces.

![SysAdmins Control Panel Rule](../../images/10-group-policy-hardening/07-sysadmins-control-panel-exception.png)

<p><sub><strong>Screenshot 133 - SysAdmins Control Panel Rule:</strong> Control Panel and PC Settings restriction disabled in the Sys_Admins policy.</sub></p>

---

### Step 09 - Deploy software through GPO

The Notepad++ 8.5 MSI is assigned under **Computer Configuration > Policies > Software Settings > Software installation** using `\\samdc2\Software\Notepad++.msi`.

> Computer-based MSI assignment installs approved software centrally. The UNC path is required because clients cannot use a package stored only on an administrator's local `C:` drive.

![Software Installation Assigned](../../images/10-group-policy-hardening/15-software-installation-assigned.png)

<p><sub><strong>Screenshot 141 - Software Installation Assigned:</strong> Notepad++ 8.5 assigned through GPO from the shared UNC package path on SAMDC2.</sub></p>

---

### Step 10 - Validate software deployment

`SAMWINPC1` shows Notepad++ installed after receiving the software-deployment policy, proving that the MSI deployed from the server share through GPO.

> The screenshots connect the GPO assignment from `\\samdc2\Software\Notepad++.msi` to the endpoint result: Notepad++ available on the Windows 10 client.

![Software Deployment Validation](../../images/10-group-policy-hardening/16-software-deployment-validation.png)

<p><sub><strong>Screenshot 142 - Software Deployment Validation:</strong> Notepad++ installed and available on SAMWINPC1 after GPO software deployment.</sub></p>

---

## Validation and Summary


Validation confirms policy links, editor settings, standard-user restrictions, `Sys_Admins` exceptions, Restricted Groups configuration, software assignment, and Notepad++ on the Windows 10 client.


This chapter shows endpoint policy enforcement through standard-user restrictions, admin exceptions, removable-media controls, local-admin targeting, and GPO software deployment.

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
