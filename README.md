# Windows Server Infrastructure Lab

![Category](https://img.shields.io/badge/Category-Windows%20Infrastructure-blue)
![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202019-lightgrey)
![Focus](https://img.shields.io/badge/Focus-Active%20Directory%20%7C%20DNS%20%7C%20DHCP%20%7C%20GPO-blueviolet)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

> Project by Samuel Kim. All rights reserved. See [LICENSE](LICENSE).

## Project Overview

This project documents a complete Windows Server domain environment built around identity, network services, remote administration, file services, and endpoint policy enforcement. The lab follows the same order an administrator would use in a real deployment: plan the topology, build the domain, add redundancy, configure network services, validate client access, and then apply security controls.

The main goal is to demonstrate how core Microsoft infrastructure components work together. Active Directory provides centralized identity, DNS allows domain and external name resolution, DHCP automates client addressing, RRAS provides lab routing/NAT, and Group Policy enforces workstation security settings. File services, roaming profiles, quotas, and software deployment are included to show how a domain environment supports daily user operations.

## Objectives

- Build a Windows domain with two domain controllers.
- Promote DC1 into a new Active Directory forest and add DC2 as an additional domain controller.
- Configure AD users, groups, OUs, FSMO role management, and scripted account creation.
- Implement RRAS NAT/PAT routing for outbound lab connectivity.
- Deploy DHCP scopes, exclusions, lease duration, gateway/DNS options, and DHCP failover.
- Configure DNS forwarding, primary/secondary zones, stub zones, host records, and round robin validation.
- Enable remote administration through RDP for approved administrator groups.
- Configure roaming and mandatory profiles.
- Implement file-server shares, NTFS permissions, mapped drives, and quotas.
- Harden Windows clients with Group Policy controls.
- Configure and apply a domain-wide password policy baseline.

## Project Chapters

| # | Chapter | Description |
|---|---------|-------------|
| 1 | [Topology and Lab Environment](docs/01-topology-and-lab-environment/README.md) | Lab topology, domain name, server roles, addressing plan, operating-system baseline, and virtualization inventory |
| 2 | [Active Directory Domain Services](docs/02-active-directory-domain-services/README.md) | Domain-controller deployment, administrative structures, scripted account creation, FSMO work, and AD replication validation |
| 3 | [NAT and Routing with RRAS](docs/03-nat-and-rras-routing/README.md) | SAMNAT routing, RRAS NAT configuration, and outbound connectivity validation |
| 4 | [DHCP Services and Failover](docs/04-dhcp-services-and-failover/README.md) | DHCP scope, exclusions, options, client lease validation, and DHCP failover |
| 5 | [Remote Administration](docs/05-remote-administration/README.md) | RDP administration, administrator group access, and lab-only NAT forwarding validation |
| 6 | [DNS Services and Name Resolution](docs/06-dns-services-and-name-resolution/README.md) | Forwarders, controlled zones, conditional forwarding, stub zones, secondary zones, host records, and round robin |
| 7 | [Roaming and Mandatory Profiles](docs/07-roaming-and-mandatory-profiles/README.md) | Roaming profile storage, profile paths, server-side profile folders, and mandatory profile conversion |
| 8 | [File Services and Access Control](docs/08-file-services-and-access-control/README.md) | File services, home folders, DATA permissions, mapped drives, and FSRM quota controls |
| 9 | [Group Policy Hardening and Software Deployment](docs/09-group-policy-hardening-and-software-deployment/README.md) | User restrictions, removable-storage controls, administrator exceptions, local administrator targeting, and MSI deployment |
| 10 | [Password Policy and Account Security](docs/10-password-policy-and-account-security/README.md) | Domain password policy baseline and account-security explanation |
| 11 | [Final Summary](docs/11-final-summary/README.md) | Validation summary, production recommendations, skills, and project closure |

## Tools and Technologies

- Microsoft Windows Server 2019
- Microsoft Windows 10
- VMware virtual machines
- Active Directory Domain Services
- DNS Server
- DHCP Server
- Routing and Remote Access Service
- Group Policy Management
- Active Directory Users and Computers
- File Server Resource Manager
- PowerShell Active Directory module
- `dsadd`
- `net use`
- RDP
- NTFS ACLs

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

