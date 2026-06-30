# Active Directory Domain Services

This chapter covers Active Directory Domain Services in the Windows Server infrastructure lab. It explains what was configured, why the configuration matters, and which evidence validates the result.

---

## Purpose

Build the identity foundation by promoting DC1, adding DC2, creating administrative structures, automating account creation, and validating directory replication.

## Technical Context

Active Directory is the foundation of the lab. DC1 is prepared first because it becomes the first domain controller in the forest and establishes the domain naming structure. Once the domain exists, DC2 is joined and promoted as an additional domain controller to demonstrate redundancy, replication, and role distribution.

This section also includes account hardening and administrative group preparation because those decisions affect how the rest of the environment is managed.

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

## Steps Covered

| Step | Description |
|------|-------------|
| Rename DC1 before promotion | The first server is renamed before domain promotion. |
| Add Active Directory Domain Services | The AD DS and DNS roles are selected from Server Manager. |
| Create the new forest | DC1 is promoted into a new forest using samueldomain.com as the root domain. |
| Controlled admin account and built-in account hardening | After domain creation, a controlled administrative account is created and used instead of relying on the default built in Administrator account. |
| Promote DC2 into the existing domain | DC2 is joined to the existing domain and promoted as an additional domain controller. |
| Transfer the RID Master role | The RID Master FSMO role is transferred to DC2 as part of the domain controller administration exercise. |
| Create administrative and department groups | The lab creates groups such as Sales and Sys Admins . |
| Create users and groups with DSADD | The lab uses dsadd to create a domain user and two security groups directly from Command Prompt. |
| Create users with PowerShell | PowerShell is used to add ten domain users, user50 through user59 , to the PS organizational unit. |
| Validate AD replication | Before comparing directory objects, DC2 is discovered through Active Directory and added to the All Servers view in Server Manager. |

---

## Detailed Walkthrough

### Step 01 - Rename DC1 before promotion

The first server is renamed before domain promotion. Clear server naming is important because the hostname becomes part of the domain infrastructure and appears in DNS, ADUC, and management tools.

> A domain controller name becomes part of many administrative views and records. Renaming the server before promotion is cleaner than renaming it after it already holds domain-controller services, DNS records, and replication metadata.

![DC1 Computer Name](../../images/03-active-directory/01-dc1-computer-name.png)

<p><sub><strong>Screenshot 006 - DC1 Computer Name:</strong> DC1 computer rename before domain promotion.</sub></p>

---

### Step 02 - Add Active Directory Domain Services

The AD DS and DNS roles are selected from Server Manager. DNS is installed with AD DS because the domain depends on DNS for locating domain controllers and services.

> Active Directory Domain Services is the Windows role that provides centralized authentication, users, groups, computers, and domain policy. DNS is installed alongside it because domain clients use DNS records to find domain controllers and logon services.

![Add AD DS Role](../../images/03-active-directory/02-add-ad-ds-role.png)

<p><sub><strong>Screenshot 007 - Add AD DS Role:</strong> Adding Active Directory Domain Services and DNS roles.</sub></p>

---

### Step 03 - Create the new forest

DC1 is promoted into a new forest using `samueldomain.com` as the root domain. This creates the directory structure, domain naming context, and initial domain services.

> A forest is the top-level security and directory boundary in Active Directory. Creating the first forest and root domain turns the server from a standalone machine into the first authority for identity, authentication, and policy in the lab.

![New Forest Root Domain](../../images/03-active-directory/04-new-forest-root-domain.png)

<p><sub><strong>Screenshot 009 - New Forest Root Domain:</strong> New forest deployment with `samueldomain.com` as the root domain.</sub></p>

---

### Step 04 - Controlled admin account and built-in account hardening

After domain creation, a controlled administrative account is created and used instead of relying on the default built-in Administrator account. At this stage, the built-in Administrator account is also disabled as part of the hardening process.

This is a security-focused change, not just an administrative preference. The built-in Administrator account has the well-known RID 500, which makes it predictable during RID enumeration and account discovery. Attackers often look for default privileged accounts first, so using a named controlled admin account and disabling or renaming the default account reduces exposure to default-user targeting while keeping administrative access available.

> Administrative work should be tied to controlled, named accounts instead of a default account that every attacker expects to exist. This also makes auditing cleaner because privileged actions can be connected to a specific administrative identity.

![Create Controlled Admin Account](../../images/03-active-directory/06-create-controlled-admin-account.png)

<p><sub><strong>Screenshot 011 - Create Controlled Admin Account:</strong> Controlled administrative account created before hardening the default built-in Administrator account.</sub></p>

> Security note: In a production environment, this control should be combined with least privilege, privileged access management, MFA for administrative access, and auditing of privileged logons.

---

### Step 05 - Promote DC2 into the existing domain

DC2 is joined to the existing domain and promoted as an additional domain controller. This improves resilience and provides another server for domain services.

> A second domain controller gives the domain another copy of the directory database and another server that can answer logon and directory requests. This reduces dependence on DC1 and demonstrates the idea of redundancy in identity infrastructure.

![Promote DC2 Existing Domain](../../images/03-active-directory/10-promote-dc2-existing-domain.png)

<p><sub><strong>Screenshot 015 - Promote DC2 Existing Domain:</strong> DC2 promotion into the existing domain.</sub></p>

---

### Step 06 - Transfer the RID Master role

The RID Master FSMO role is transferred to DC2 as part of the domain-controller administration exercise. The RID Master allocates relative IDs used to create unique SIDs for new AD objects.

> FSMO means Flexible Single Master Operations. These are special Active Directory roles that must be handled by one domain controller at a time to avoid conflicts. The RID Master gives domain controllers pools of Relative IDs, and those RIDs become part of each user, group, or computer SID. Without available RID pools, the domain can run into problems creating new security principals.

![Transfer RID Master to DC2](../../images/03-active-directory/13-transfer-rid-master-to-dc2.png)

<p><sub><strong>Screenshot 018 - Transfer RID Master to DC2:</strong> RID Master role transfer confirmation.</sub></p>

---

### Step 07 - Create administrative and department groups

The lab creates groups such as `Sales` and `Sys_Admins`. These groups are later used for file permissions, remote management, and Group Policy targeting.

> Groups are the clean way to manage access. Instead of assigning permissions one user at a time, users are placed into groups and permissions are assigned to the group, which is easier to audit and maintain.

![Sales and SysAdmins Groups](../../images/03-active-directory/15-sales-and-sysadmins-groups.png)

<p><sub><strong>Screenshot 020 - Sales and SysAdmins Groups:</strong> Sales and Sys_Admins group membership.</sub></p>

---

### Step 08 - Create users and groups with DSADD

The lab uses `dsadd` to create a domain user and two security groups directly from Command Prompt. Each command identifies the Active Directory object by its distinguished name, which includes the object name, container, and domain path.

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

> `dsadd` demonstrates repeatable administration without relying on the graphical console. The password and non-expiring setting are retained for this isolated lab; production accounts should use protected credential handling, password expiration, and least-privilege group membership.

![DSADD Script Run](../../images/03-active-directory/17-dsadd-users-and-groups-script-run.png)

<p><sub><strong>Screenshot 022 - DSADD Script Run:</strong> DSADD script execution and created groups/users.</sub></p>

---

### Step 09 - Create users with PowerShell

PowerShell is used to add ten domain users, `user50` through `user59`, to the `PS` organizational unit. These accounts can then be assigned to security groups and used in later administration, access-control, Group Policy, and service-validation tasks.

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

Before comparing directory objects, DC2 is discovered through Active Directory and added to the **All Servers** view in Server Manager. This gives the administrator a central console for monitoring and opening management tools against both domain controllers.

> Adding DC2 to Server Manager does not enable replication by itself. AD DS replication is established when DC2 is promoted as an additional domain controller; this screen confirms that the server can be discovered in the domain and added to the management console before replication is checked in ADUC.

![Add DC2 to Server Manager](../../images/03-active-directory/21-server-manager-add-dc2.png)

<p><sub><strong>Screenshot 026 - Add DC2 to Server Manager:</strong> DC2 discovered through Active Directory and selected for addition to Server Manager.</sub></p>

Active Directory Users and Computers is opened separately against `samdc2.samueldomain.com` and `samdc1.samueldomain.com`. The `PS` organizational unit and the same user objects are visible through both domain controllers, confirming that the directory changes replicated between DC1 and DC2.

The directory view also contains `user60`, an additional test account outside the ten-account PowerShell loop documented above. The loop itself intentionally creates `user50` through `user59`.

> Replication is what keeps domain controllers synchronized. If users or groups appear on one domain controller but not another, clients may receive inconsistent authentication or policy results depending on which domain controller they contact.

![AD Replication Visible on Both DCs](../../images/03-active-directory/22-ad-replication-visible-on-both-dcs.png)

<p><sub><strong>Screenshot 027 - AD Replication Visible on Both DCs:</strong> The PS organizational unit and matching user objects visible through both DC2 and DC1.</sub></p>

---

## Validation

Validation is based on AD DS role screens, domain promotion evidence, FSMO role transfer evidence, account and group creation, Server Manager discovery, and Active Directory Users and Computers views against both domain controllers.

## Chapter Summary

This chapter creates the central identity layer for the lab. DC1 and DC2 provide domain services, DNS integration, administrative accounts, groups, scripted users, and replicated directory objects used by the following service chapters.

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

