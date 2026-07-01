# Final Summary

This lab demonstrates a complete Windows Server domain environment built around identity, network services, remote administration, file services, endpoint policy enforcement, and client validation. The result is a working `samueldomain.com` domain with two domain controllers, centralized DNS and DHCP, RRAS NAT routing, profile management, shared storage, Group Policy hardening, software deployment, and domain password policy controls.

The project also separates lab evidence from production design. Screenshots and commands validate that the configuration worked in the isolated training environment, while the recommendations below document where production deployments would require stronger security, monitoring, segmentation, and credential handling.

## Validation Summary

| Area | Validation captured |
|------|---------------------|
| Domain deployment | DC1 promoted into a new forest and DC2 added as an additional domain controller |
| AD replication | Created users, groups, and OUs visible across both domain controllers |
| Account administration | Controlled administrator account created and built-in Administrator hardening documented |
| RRAS NAT | Internal systems reached external networks through SAMNAT |
| DHCP | Windows 10 client received automatic addressing, gateway, DNS, and lease settings |
| DHCP failover | DC2 configured as DHCP failover partner |
| DNS | Forwarders, controlled zones, conditional forwarder, stub zone, secondary zone, host record, and round robin records documented and tested |
| Remote administration | RDP access validated from the Windows client and through a lab-only NAT forwarding rule |
| Profiles | Roaming profile storage and mandatory profile behavior validated |
| File services | Home folders, DATA permissions, mapped drive behavior, and FSRM quota controls documented |
| Group Policy | Standard-user restrictions, administrator exceptions, removable-storage controls, local administrator targeting, and MSI deployment validated |
| Password policy | Domain password baseline documented through the linked `PassPolicy` GPO |

## Production Recommendations

- Keep the visible domain names, usernames, internal addresses, and lab passwords only as isolated training data. Do not reuse them as production credentials.
- Use named administrator accounts, least privilege, MFA, privileged-access management, and logon auditing for all administrative work.
- The built-in Administrator account has the well-known RID 500 and is commonly targeted. Disabling or renaming it after creating controlled named administrator accounts reduces predictable exposure.
- Use VPN, RD Gateway, MFA, source-IP restrictions, firewall rules, and privileged-access monitoring instead of direct RDP port forwarding.
- Keep Windows Defender Firewall enabled and allow required services through explicit inbound rules.
- Use least-privilege Active Directory security groups for file access. Broad `Everyone` permissions are acceptable only for isolated lab demonstrations.
- Treat DHCP failover as DHCP lease-state and service-continuity protection, not as a general-purpose Windows failover cluster.
- Use reliable NTP and consistent time settings before treating logs as investigation-ready evidence.
- For password policy, combine length and complexity with compromised-password screening, MFA, account lockout protection, monitoring, and help-desk procedures that do not block legitimate password changes.
- DNS round robin can rotate multiple A-record responses, but it does not perform health checks and should not be treated as full load balancing.
- Conditional forwarders and stub zones are useful in enterprise DNS designs, but production use should reflect trusted namespaces, partner domains, or separate AD forests rather than public-domain lab examples.
- GPO computer settings should be linked or filtered so they apply to computer accounts, not only to OUs containing user accounts.

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
| 11 | [Final Summary](README.md) | Validation summary, production recommendations, skills, and project closure |
