# Group Policy Hardening and Software Deployment

This chapter covers Group Policy Hardening and Software Deployment in the Windows Server infrastructure lab. It explains what was configured, why the configuration matters, and which evidence validates the result.


## Technical Context

Group Policy is used to restrict standard users, add `Sys_Admins` to local Administrators, and deploy software.

This section demonstrates centralized endpoint control. Instead of configuring each workstation manually, GPOs apply consistent restrictions and settings based on domain structure and security groups.

The hardening focus is to reduce the standard user's ability to change workstation behavior, launch administrative tools, or move data through removable media.

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

The Group Policy Management console provides centralized control for domain-linked policies.

> Group Policy Management is where administrators create, link, and review GPOs. A GPO, or Group Policy Object, is a collection of settings that can be applied to users or computers in the domain.

![Group Policy Management Opened](../../images/10-group-policy-hardening/01-group-policy-management-opened.png)

<p><sub><strong>Screenshot 127 - Group Policy Management Opened:</strong> Group Policy Management opened.</sub></p>

---

### Step 02 - Create a client-hardening GPO

A new GPO is created for client restrictions such as Command Prompt, Control Panel, and removable storage controls.

> A dedicated hardening GPO keeps security settings organized and easier to review. It also separates workstation restrictions from unrelated domain policies, which makes troubleshooting cleaner.

![Create New Hardening GPO](../../images/10-group-policy-hardening/03-create-new-hardening-gpo.png)

<p><sub><strong>Screenshot 129 - Create New Hardening GPO:</strong> Creating a new hardening GPO.</sub></p>

---

### Step 03 - Restrict Command Prompt access

Inside **User Configuration > Policies > Administrative Templates > System**, the **Prevent access to the command prompt** setting is enabled in the `BLOCK CMD & CP` GPO. This prevents targeted standard users from launching `cmd.exe` and reduces their ability to run interactive command-line tools or unapproved batch commands.

> Blocking Command Prompt is an endpoint restriction, not a complete application-control solution. PowerShell, scripts, and other interpreters should be governed separately when stronger execution control is required, for example through AppLocker or Windows Defender Application Control.

![Command Prompt Access Blocked](../../images/10-group-policy-hardening/05-command-prompt-deny-policy.png)

<p><sub><strong>Screenshot 131 - Command Prompt Access Blocked:</strong> Prevent access to the command prompt enabled in the BLOCK CMD & CP GPO.</sub></p>

---

### Step 04 - Restrict access to Control Panel and PC settings

Inside **User Configuration > Policies > Administrative Templates > Control Panel**, the **Prohibit access to Control Panel and PC settings** policy is enabled. This prevents standard users from opening both the traditional Control Panel and the Windows Settings application through `Control.exe` and `SystemSettings.exe`.

> Centralizing this restriction helps prevent users from changing system options that could weaken the workstation baseline, interfere with support, or bypass other administrative controls. Because it is a user-side policy, it follows the targeted domain user when that user signs in to an affected workstation.

![Control Panel and PC Settings Restricted](../../images/10-group-policy-hardening/04-hardening-gpo-editor-settings.png)

<p><sub><strong>Screenshot 130 - Control Panel and PC Settings Restricted:</strong> Prohibit access to Control Panel and PC settings enabled in the BLOCK CMD & CP GPO.</sub></p>

---

### Step 05 - Validate standard-user restriction

The standard user is blocked from accessing Control Panel, confirming that the hardening policy applies.

> GPO settings are only valuable if they actually apply to the intended users and computers. This test confirms that standard users receive the restriction and cannot bypass the baseline through Control Panel.

![Standard User Control Panel Blocked](../../images/10-group-policy-hardening/12-standard-user-control-panel-blocked.png)

<p><sub><strong>Screenshot 138 - Standard User Control Panel Blocked:</strong> Standard user blocked from Control Panel.</sub></p>

---

### Step 06 - Configure removable storage restrictions

The `BLOCK Disk On Key` GPO is linked at the domain level so the removable-storage restriction can be processed by the targeted domain users and computers.

![Block Disk On Key GPO Link](../../images/10-group-policy-hardening/09-gpo-linking-overview.png)

<p><sub><strong>Screenshot 135 - Block Disk On Key GPO Link:</strong> BLOCK Disk On Key GPO linked beneath the SamuelDomain.com domain.</sub></p>

The removable-storage policy denies access to removable media. This helps reduce data exfiltration, unauthorized copying, and the introduction of untrusted files from USB storage devices.

> Removable media can be used to copy data out of the environment or introduce untrusted files. Blocking it through policy is a common endpoint-hardening control for standard users.

![Removable Storage Deny Policy](../../images/10-group-policy-hardening/08-removable-storage-deny-policy.png)

<p><sub><strong>Screenshot 134 - Removable Storage Deny Policy:</strong> Removable storage deny policy.</sub></p>

---

### Step 07 - Add Sys_Admins to local Administrators

The `Add to Local Admins` GPO is linked under `Sys_Admins` in Group Policy Management. This screenshot records where the policy was linked before its Restricted Groups setting is reviewed.

![Add to Local Admins GPO](../../images/10-group-policy-hardening/10-add-sysadmins-to-local-admins-gpo.png)

<p><sub><strong>Screenshot 136 - Add to Local Admins GPO:</strong> Add to Local Admins GPO linked beneath the Sys_Admins OU.</sub></p>

Restricted Groups are configured with the intention of placing `Sys_Admins` into the local Administrators group on managed workstations. Because this setting is under **Computer Configuration**, the GPO must be linked where the target computer accounts are located, or otherwise scoped so those computers receive it.

> Restricted Groups can enforce local group membership from the domain. Linking the GPO only to an OU that contains administrator user accounts does not apply the computer-side setting to workstations. Correct validation should include `gpresult /scope computer /r` and `net localgroup administrators` on a target client.

> Security note: Local administrator membership should be limited, reviewed, and monitored. In production, this kind of access should be tied to least privilege and privileged-access procedures.

![Restricted Groups Membership](../../images/10-group-policy-hardening/11-restricted-groups-membership.png)

<p><sub><strong>Screenshot 137 - Restricted Groups Membership:</strong> Restricted Groups membership configuration.</sub></p>

---

### Step 08 - Apply Sys_Admins workstation rules

The `Allow CMD & CP` GPO is linked beneath `Sys_Admins`. It provides a separate user-policy scope for approved administrators who need Command Prompt and Control Panel access for support and troubleshooting.

> This is an exception policy, not a contradiction of the standard-user restriction. The baseline GPO blocks risky tools for regular users, while the Sys_Admins policy keeps those tools available for administrators who need them to diagnose systems, apply fixes, and verify domain settings.

![SysAdmins GPO Link](../../images/10-group-policy-hardening/02-gpo-list-overview.png)

<p><sub><strong>Screenshot 128 - SysAdmins GPO Link:</strong> Allow CMD & CP GPO linked beneath the Sys_Admins OU.</sub></p>

Inside this policy, **Prevent access to the command prompt** is set to **Disabled**. Disabling the restriction means the policy does not block `cmd.exe` for the targeted Sys_Admins users.

![SysAdmins Command Prompt Rule](../../images/10-group-policy-hardening/06-sysadmins-command-prompt-exception.png)

<p><sub><strong>Screenshot 132 - SysAdmins Command Prompt Rule:</strong> Command Prompt restriction disabled in the Sys_Admins policy.</sub></p>

The **Prohibit access to Control Panel and PC settings** setting is also disabled in the same administrative policy, allowing the targeted administrators to open management interfaces required for their work.

![SysAdmins Control Panel Rule](../../images/10-group-policy-hardening/07-sysadmins-control-panel-exception.png)

<p><sub><strong>Screenshot 133 - SysAdmins Control Panel Rule:</strong> Control Panel and PC Settings restriction disabled in the Sys_Admins policy.</sub></p>

---

### Step 09 - Deploy software through GPO

The Notepad++ 8.5 MSI package is assigned under **Computer Configuration > Policies > Software Settings > Software installation**. The package uses the UNC source `\\samdc2\Software\Notepad++.msi`, allowing targeted domain computers to reach the same network location while Group Policy is processed.

> Assigning an MSI through Computer Configuration allows Windows to install an approved application centrally instead of requiring a manual installation on every endpoint. A UNC path is essential because client computers cannot access a package stored only on the administrator's local `C:` drive.

![Software Installation Assigned](../../images/10-group-policy-hardening/15-software-installation-assigned.png)

<p><sub><strong>Screenshot 141 - Software Installation Assigned:</strong> Notepad++ 8.5 assigned through GPO from the shared UNC package path on SAMDC2.</sub></p>

---

### Step 10 - Validate software deployment

The Windows 10 client `SAMWINPC1` shows Notepad++ installed after the computer received the software-deployment policy. Together with the preceding GPO configuration, this provides end-to-end evidence that the MSI was assigned from the server share and successfully deployed to the target workstation through Group Policy.

> This validation connects the administrative configuration to the endpoint result: the first screenshot shows the package assigned through GPO from `\\samdc2\Software\Notepad++.msi`, and the second shows Notepad++ available on the Windows 10 client.

![Software Deployment Validation](../../images/10-group-policy-hardening/16-software-deployment-validation.png)

<p><sub><strong>Screenshot 142 - Software Deployment Validation:</strong> Notepad++ installed and available on SAMWINPC1 after GPO software deployment.</sub></p>

---

## Validation and Summary


Validation is based on Group Policy Management screens, policy editor settings, standard-user restriction evidence, Sys_Admins exception settings, Restricted Groups configuration, software assignment, and Notepad++ visibility on the Windows 10 client.


This chapter centralizes endpoint policy enforcement through GPO. It shows standard-user restrictions, administrator exceptions, removable-media controls, local-admin targeting considerations, and GPO-based software deployment.

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
