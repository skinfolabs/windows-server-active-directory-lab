# Final Summary

This lab demonstrates a Windows Server domain environment built around identity, network services, remote administration, file services, endpoint policy enforcement, and client validation. The result is a working `samueldomain.com` domain with two domain controllers, centralized DNS and DHCP, RRAS NAT routing, profile management, shared storage, Group Policy hardening, software deployment, and password policy controls.

Screenshots and commands validate the configuration in an isolated lab. The recommendations below identify where production deployments would need stronger security, monitoring, segmentation, and credential handling.

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

- Keep visible domain names, usernames, internal addresses, and lab passwords as isolated training data only.
- Use named administrator accounts with least privilege, MFA, privileged-access management, and logon auditing.
- Disable or rename the built-in Administrator account after creating controlled named admin accounts. Its RID 500 identity is predictable and commonly targeted.
- Use VPN, RD Gateway, MFA, source-IP restrictions, firewall rules, and monitoring instead of direct RDP port forwarding.
- Keep Windows Defender Firewall enabled and allow required services through explicit inbound rules.
- Use least-privilege Active Directory security groups for file access. Broad `Everyone` permissions are acceptable only for isolated lab demonstrations.
- Treat DHCP failover as lease-state and service-continuity protection, not as a general-purpose Windows failover cluster.
- Use reliable NTP and consistent time settings before treating logs as investigation-ready evidence.
- Combine password length and complexity with compromised-password screening, MFA, account lockout protection, monitoring, and clear help-desk procedures.
- DNS round robin can rotate multiple A-record responses, but it does not perform health checks and should not be treated as full load balancing.
- Use conditional forwarders and stub zones for trusted namespaces, partner domains, or separate AD forests.
- GPO computer settings should be linked or filtered so they apply to computer accounts, not only to OUs containing user accounts.

## Skills Demonstrated

- Windows Server administration
- Active Directory forest, domain, and replication management
- FSMO role awareness
- User, group, and OU management
- PowerShell automation for AD object creation
- Legacy `dsadd` command usage
- RRAS NAT/PAT configuration
- DHCP scope and failover configuration
- DNS forwarding, zones, host records, and round robin
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
| 11 | [Final Summary](README.md) |
