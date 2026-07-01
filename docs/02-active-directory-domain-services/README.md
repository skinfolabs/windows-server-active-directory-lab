# Active Directory Domain Services

This chapter documents the Active Directory buildout for the lab: domain creation, controller redundancy, account administration, FSMO practice, and replication validation.


## Technical Context

Active Directory is the lab's identity foundation. DC1 becomes the first domain controller and creates the forest, while DC2 is later promoted to demonstrate redundancy, replication, and role distribution.

Account hardening and administrative groups are included because they define how the rest of the environment is managed.

**Implemented controls:**

- Promoted DC1 into a new Active Directory forest.
- Added DC2 as an additional domain controller.
- Created a controlled administrator account and documented built-in Administrator hardening.
- Created administrative and departmental groups.
- Demonstrated user creation with DSADD and PowerShell.
- Validated AD object replication between domain controllers.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| AD DS | Active Directory Domain Services, the Windows role that provides domain identity, users, groups, computers, and policy. |
| Forest | The top-level Active Directory boundary created when the first domain is promoted. |
| FSMO | Flexible Single Master Operations roles that are held by one domain controller at a time to avoid conflicting changes. |
| RID Master | FSMO role that allocates Relative IDs used to create unique SIDs for new security principals. |
| Replication | Synchronization of directory objects between domain controllers. |

---

## Detailed Walkthrough

### Step 01 - Rename DC1 before promotion

DC1 is renamed before domain promotion so the hostname is clean before it appears in DNS, ADUC, and management tools.

> Renaming before promotion avoids changing a server after it already holds domain-controller services, DNS records, and replication metadata.

![DC1 Computer Name](../../images/03-active-directory/01-dc1-computer-name.png)

<p><sub><strong>Screenshot 006 - DC1 Computer Name:</strong> DC1 computer rename before domain promotion.</sub></p>

---

### Step 02 - Add Active Directory Domain Services

The AD DS and DNS roles are selected from Server Manager. DNS is installed with AD DS because the domain depends on DNS for locating domain controllers and services.

> AD DS provides centralized authentication, users, groups, computers, and policy. DNS is installed with it because domain clients use DNS records to find domain controllers and logon services.

![Add AD DS Role](../../images/03-active-directory/02-add-ad-ds-role.png)

<p><sub><strong>Screenshot 007 - Add AD DS Role:</strong> Adding Active Directory Domain Services and DNS roles.</sub></p>

---

### Step 03 - Create the new forest

DC1 is promoted into a new forest using `samueldomain.com` as the root domain. This creates the directory structure, domain naming context, and initial domain services.

> A forest is the top-level Active Directory boundary. Creating the first forest turns DC1 into the first authority for identity, authentication, and policy in the lab.

![New Forest Root Domain](../../images/03-active-directory/04-new-forest-root-domain.png)

<p><sub><strong>Screenshot 009 - New Forest Root Domain:</strong> New forest deployment with `samueldomain.com` as the root domain.</sub></p>

---

### Step 04 - Controlled admin account and built-in account hardening

After domain creation, a controlled admin account is created and used instead of the default built-in Administrator account. The built-in account is also disabled as part of hardening.

The built-in Administrator account has the well-known RID 500, making it predictable during RID enumeration and account discovery. Using a named admin account and disabling or renaming the default account reduces default-user targeting while preserving administrative access.

> Named admin accounts make privileged access less predictable and easier to audit than a default account every attacker expects to exist.

![Create Controlled Admin Account](../../images/03-active-directory/06-create-controlled-admin-account.png)

<p><sub><strong>Screenshot 011 - Create Controlled Admin Account:</strong> Controlled administrative account created before hardening the default built-in Administrator account.</sub></p>

> Security note: In production, combine this with least privilege, PAM, MFA, and privileged-logon auditing.

---

### Step 05 - Promote DC2 into the existing domain

DC2 is joined to the domain and promoted as an additional domain controller for resilience and service redundancy.

> A second domain controller stores another directory copy and can answer logon and directory requests, reducing dependence on DC1.

![Promote DC2 Existing Domain](../../images/03-active-directory/10-promote-dc2-existing-domain.png)

<p><sub><strong>Screenshot 015 - Promote DC2 Existing Domain:</strong> DC2 promotion into the existing domain.</sub></p>

---

### Step 06 - Transfer the RID Master role

The RID Master FSMO role is transferred to DC2. This role allocates relative IDs used to create unique SIDs for new AD objects.

> FSMO means Flexible Single Master Operations: special AD roles handled by one domain controller at a time. The RID Master issues Relative ID pools used in user, group, and computer SIDs.

![Transfer RID Master to DC2](../../images/03-active-directory/13-transfer-rid-master-to-dc2.png)

<p><sub><strong>Screenshot 018 - Transfer RID Master to DC2:</strong> RID Master role transfer confirmation.</sub></p>

---

### Step 07 - Create administrative and department groups

The lab creates groups such as `Sales` and `Sys_Admins`. These groups are later used for file permissions, remote management, and Group Policy targeting.

> Groups make access easier to audit and maintain because permissions are assigned to roles instead of individual users.

![Sales and SysAdmins Groups](../../images/03-active-directory/15-sales-and-sysadmins-groups.png)

<p><sub><strong>Screenshot 020 - Sales and SysAdmins Groups:</strong> Sales and Sys_Admins group membership.</sub></p>

---

### Step 08 - Create users and groups with DSADD

The lab uses `dsadd` to create a domain user and two security groups from Command Prompt. Each object is identified by its distinguished name, including object name, container, and domain path.

```cmd
dsadd user "CN=David Scotch,CN=Users,DC=SamuelDomain,DC=com" ^
    -samid davids ^
    -upn davids@samueldomain.com ^
    -fn David ^
    -ln Scotch ^
    -pwd 1234qweR ^
    -pwdneverexpires yes

dsadd group "CN=Group 1,CN=Users,DC=SamuelDomain,DC=com" ^
    -scope g ^
    -secgrp yes ^
    -members "CN=Shon Wiskey,CN=Users,DC=SamuelDomain,DC=com"

dsadd group "CN=Group 2,CN=Users,DC=SamuelDomain,DC=com" ^
    -scope g ^
    -secgrp yes ^
    -members "CN=David Scotch,CN=Users,DC=SamuelDomain,DC=com"
```

> `dsadd` demonstrates repeatable administration outside the GUI. The password and non-expiring setting are kept for this isolated lab; production accounts need protected credential handling and least privilege.

![DSADD Script Run](../../images/03-active-directory/17-dsadd-users-and-groups-script-run.png)

<p><sub><strong>Screenshot 022 - DSADD Script Run:</strong> DSADD script execution and created groups/users.</sub></p>

---

### Step 09 - Create users with PowerShell

PowerShell adds ten domain users, `user50` through `user59`, to the `PS` OU for later access-control, Group Policy, and service-validation tasks.

> Automating account creation keeps usernames, UPNs, OU placement, and initial account settings consistent. The `-lt 60` condition stops the loop before 60, producing exactly ten accounts.

```powershell
Import-Module ActiveDirectory

$domainDn = "DC=SamuelDomain,DC=com"
$ouName = "PS"
$ouPath = "OU=$ouName,$domainDn"
$initialPassword = ConvertTo-SecureString "1234qweR" -AsPlainText -Force

if (-not (Get-ADOrganizationalUnit -Identity $ouPath -ErrorAction SilentlyContinue)) {
    New-ADOrganizationalUnit `
        -Name $ouName `
        -Path $domainDn `
        -ProtectedFromAccidentalDeletion $true
}

for ($i = 50; $i -lt 60; $i++) {
    $username = "user$i"
    $displayName = "User $i"

    New-ADUser `
        -Name $displayName `
        -DisplayName $displayName `
        -SamAccountName $username `
        -UserPrincipalName "$username@samueldomain.com" `
        -Path $ouPath `
        -AccountPassword $initialPassword `
        -Enabled $true `
        -ChangePasswordAtLogon $true
}
```

---

### Step 10 - Validate AD replication

Before comparing directory objects, DC2 is discovered through Active Directory and added to **All Servers** in Server Manager.

> Adding DC2 to Server Manager does not create replication; promotion does. This screen only confirms that DC2 is discoverable and manageable before ADUC validation.

![Add DC2 to Server Manager](../../images/03-active-directory/21-server-manager-add-dc2.png)

<p><sub><strong>Screenshot 026 - Add DC2 to Server Manager:</strong> DC2 discovered through Active Directory and selected for addition to Server Manager.</sub></p>

ADUC is opened against both `samdc2.samueldomain.com` and `samdc1.samueldomain.com`. The `PS` OU and matching users appear through both controllers, confirming replication.

The view also contains `user60`, an extra test account outside the documented PowerShell loop. The loop intentionally creates `user50` through `user59`.

> Replication keeps domain controllers synchronized so clients receive consistent authentication and policy results regardless of which controller they contact.

![AD Replication Visible on Both DCs](../../images/03-active-directory/22-ad-replication-visible-on-both-dcs.png)

<p><sub><strong>Screenshot 027 - AD Replication Visible on Both DCs:</strong> The PS organizational unit and matching user objects visible through both DC2 and DC1.</sub></p>

---

## Validation and Summary


Validation confirms AD DS role installation, domain promotion, FSMO transfer practice, object creation, Server Manager discovery, and ADUC visibility on both domain controllers.


This chapter creates the lab's identity layer: DC1 and DC2, DNS integration, admin accounts, groups, scripted users, and replicated AD objects.

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
