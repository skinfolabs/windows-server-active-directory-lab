# Testing, Results, and Recommendations

This chapter summarizes the completed validation work, final lab results, demonstrated skills, production considerations, and remaining evidence limits. The recommendations are kept in the final report so the project remains self-contained.

---

## Testing and Verification

- Domain join was validated for Windows Server and Windows 10 systems.
- Active Directory object creation was validated in Active Directory Users and Computers.
- AD synchronization was validated by viewing created objects across both domain controllers.
- Internet connectivity was validated from internal systems through SAMNAT using ping tests.
- DHCP assignment was validated on the Windows 10 client with automatically assigned IP configuration.
- DHCP failover was configured with DC2 as the partner server.
- DNS forwarding and zone behavior were validated with `nslookup`, DNS Manager, and ping tests.
- RDP access was validated from the Windows 10 workstation and through a lab NAT forwarding rule.
- Roaming and mandatory profile behavior was validated through Windows user profile views and server-side profile folders.
- File share permissions were validated through successful and blocked user actions.
- The FSRM hard quota was configured for storage governance.
- GPO hardening was validated by standard-user restrictions and Sys_Admins exception testing.
- Notepad++ was visible on the Windows 10 client after the software-deployment exercise.
- The domain password baseline and its domain-level GPO link were documented.

## Results

The lab produced a working Windows domain infrastructure with redundant domain controller services, centralized DNS and DHCP, outbound NAT routing, domain-based remote administration, profile management, shared file services, GPO hardening, software deployment, and password policy controls.

## Skills Demonstrated

- Windows Server administration
- Active Directory forest and domain deployment
- Domain controller promotion and replication validation
- FSMO role awareness
- User, group, and OU management
- PowerShell automation for AD object creation
- Legacy `dsadd` command usage
- RRAS NAT/PAT configuration
- DHCP scope and failover configuration
- DNS forwarding, zones, host records, stub zones, and round robin
- Remote administration with RDP
- Roaming and mandatory profile management
- NTFS and share permission design
- Mapped drive scripting
- File Server Resource Manager quotas
- Group Policy hardening
- GPO software deployment
- Password policy administration

## Production Considerations and Evidence Limits

### Repository Scope

This repository documents the Windows Server lab workflow, configuration decisions, commands, and validation results. It does not include virtual-machine exports or live server configuration backups.

### Lab Data

The screenshots retain the lab domain, internal IP addresses, test usernames, and lab passwords used during configuration. These values belong to the isolated training environment and must not be reused as production credentials.

### Image Organization

The repository contains 145 screenshots organized in technical order across these topics:

- Topology
- Lab environment
- Active Directory
- NAT/RRAS routing
- DHCP
- Remote management
- DNS
- Roaming profiles
- File server
- Group Policy hardening
- Password policy

Screenshots are preserved by default, organized by chapter, and placed near the relevant explanation whenever they support the guided walkthrough. Each screenshot keeps a stable sequence number and a caption explaining what is visible and what action or result it documents. Supplementary screenshots remain indexed in `IMAGE_MANIFEST.md` so the complete evidence set is still available without turning the walkthrough into a gallery.

### Administrative Automation

#### PowerShell bulk user creation

The documented loop creates exactly ten accounts, from `user50` through `user59`:

```powershell
for ($i = 50; $i -lt 60; $i++)
```

PowerShell is the preferred automation method for repeatable Active Directory administration. Initial passwords should be protected, changed at first logon, and never embedded in production scripts.

#### DSADD

`dsadd` is retained as a command-line administration exercise. It demonstrates distinguished names and scripted object creation, but modern administration should normally use the Active Directory PowerShell module.

### Networking and Remote Access

#### NAT and PAT

PAT is a NAT translation method that allows multiple internal sessions to share an external address by tracking transport-layer ports. It is not a standalone network protocol.

#### DHCP failover

The two DHCP servers use a DHCP failover relationship. This provides lease-state synchronization and service continuity without describing the configuration as a general-purpose failover cluster.

#### RDP forwarding

The RDP connection test is initiated from the physical host and reaches the lab through SAMNAT's WAN interface. Direct RDP port forwarding is suitable only for this isolated demonstration. Production remote access should use VPN or RD Gateway, MFA, source-IP restrictions, firewall rules, and privileged-access monitoring.

#### Windows Firewall

Windows Defender Firewall should remain enabled in production. Required services should be permitted with explicit inbound rules instead of disabling host protection.

### Identity and User Profiles

An Active Directory user account provides identity and authentication. A Windows user profile contains the user's desktop environment, registry settings, and profile data loaded during sign-in.

Mandatory profiles use `NTUSER.MAN` so profile changes are discarded at sign-out. They are useful for shared or controlled workstations but should be combined with appropriate file storage and user-data policies.

### File Services

Active Directory provides identities and group membership, while NTFS ACLs and share permissions enforce file access. FSRM provides the storage quota used in this lab.

Broad `Everyone` permissions are used only in isolated lab demonstrations. Production shares should assign access through least-privilege Active Directory security groups.

### Group Policy

Computer Configuration settings apply according to the location and scope of computer accounts. A Restricted Groups policy intended for workstations should therefore be linked to an OU containing those workstations, or scoped through an appropriate domain link and security filtering. Linking it only to an OU containing administrator users does not apply the computer-side setting to client computers.

The Notepad++ package is assigned through GPO from the network-accessible path `\\samdc2\Software\Notepad++.msi`. The Group Policy Management Editor screenshot records the assigned MSI and its UNC source, while the Windows 10 client screenshot shows Notepad++ available on `SAMWINPC1` after deployment. Together they document the server-side configuration and the resulting client installation.

### DNS

#### Mail record

The `mail.samueldomain.com` entry is a Host (`A`) record mapped directly to `192.168.116.200`. A CNAME would instead map the `mail` alias to a canonical hostname such as `samdc1.samueldomain.com`.

#### Conditional forwarder

The `google.com` conditional forwarder demonstrates domain-specific forwarding. Production conditional forwarders are commonly used for private partner namespaces, trusted networks, or separate Active Directory forests.

#### Stub zone

The `yahoo.com` stub zone stores SOA, NS, and required glue records so the DNS server can locate the zone's authoritative name servers without holding a complete copy of the zone.

#### Round robin

DNS round robin rotates the order of multiple addresses registered under one hostname. It can provide basic distribution but does not perform health checks and should not be treated as a full load-balancing solution.

### Password and Privileged Account Controls

The domain password baseline is defined in the `PassPolicy` GPO and linked at the `SamuelDomain.com` domain level. Password history, minimum length, complexity, and age requirements reduce common credential risks, but production policy should also follow current organizational and platform guidance. Long password phrases, compromised-password screening, MFA, account lockout protection, and monitoring remain important complementary controls.

The lab uses a 30-day minimum password age to demonstrate history-bypass prevention. This is restrictive for normal operations, so a production value should be selected carefully to avoid preventing legitimate password changes after user concern or help-desk intervention.

The built-in Administrator account has the well-known RID 500 and is commonly targeted during account discovery and password attacks. Disabling or renaming it after creating controlled named administrator accounts reduces predictable exposure. Production protection should also include least privilege, MFA, privileged-access management, and logon auditing.

## Final Assessment

The lab is valid as a Windows Server infrastructure portfolio project. The evidence demonstrates domain deployment, service configuration, policy enforcement, client validation, and practical production caveats. Items such as direct RDP port forwarding, broad lab permissions, visible lab credentials, and strict exercise password settings are documented as training-environment choices rather than production recommendations.

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

