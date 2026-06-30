# Windows Server Infrastructure Lab

![Category](https://img.shields.io/badge/Category-Windows%20Infrastructure-blue)
![Platform](https://img.shields.io/badge/Platform-Windows%20Server%202019-lightgrey)
![Focus](https://img.shields.io/badge/Focus-Active%20Directory%20%7C%20DNS%20%7C%20DHCP%20%7C%20GPO-blueviolet)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

> Project by Samuel Kim. All rights reserved. See [LICENSE](LICENSE).

## Project Overview

This project documents a complete Windows Server domain environment built around identity, network services, remote administration, file services, and endpoint policy enforcement. The lab follows the same order an administrator would use in a real deployment: plan the topology, build the domain, add redundancy, configure network services, validate client access, and then apply security controls.

The main goal is to demonstrate how core Microsoft infrastructure components work together. Active Directory provides centralized identity, DNS allows domain and external name resolution, DHCP automates client addressing, RRAS provides lab routing/NAT, and Group Policy enforces workstation security settings. File services, roaming profiles, quotas, and software deployment are included to show how a domain environment supports daily user operations.

## Network Topology

The lab starts with a defined topology and addressing plan. This stage matters because Windows domain services depend heavily on predictable DNS, stable domain-controller addresses, and clear server roles. A weak plan at this point can cause problems later with domain joins, DHCP options, replication, and name resolution.

The design uses two domain controllers for identity services, SAMNAT for network routing/NAT, and one Windows 10 endpoint to test the user experience from a client perspective.

**Implemented controls:**

- Defined the logical topology and traffic path.
- Documented server roles before installation.
- Planned static IP addressing for infrastructure systems.
- Prepared the VMware virtual machines required for the lab.

This diagram shows the relationship between DC1, DC2, SAMNAT, the internal LAN, the Windows 10 client, and the external internet path. It is the baseline architecture used throughout the project.

> A topology diagram makes the lab easier to understand before any configuration starts. It shows which machines provide identity services, which server routes traffic, where clients sit, and how traffic is expected to move between the internal domain and the outside network.

![Network Topology](images/01-topology/01-network-topology.png)

<p><sub><strong>Screenshot 001 - Network Topology:</strong> Network topology with DC1, DC2, NAT server, Win10 client, LAN and internet path.</sub></p>

The topology drawing labels the NAT server as `SRV1`. In the configured lab, this same routing server is named `SAMNAT`.

Before installation, the lab defines hostnames, IP addresses, gateway settings, DNS settings, and server roles. This prevents later conflicts and makes the implementation easier to validate. The table below presents the planning data as a cleaner technical reference.

**General lab parameters:**

| Item | Value |
|------|-------|
| Domain | `samueldomain.com` |
| FQDN | `samueldomain.com` |
| Internal subnet | `192.168.116.0/24` |
| DHCP address pool | `192.168.116.50` - `192.168.116.100` |
| Admin username | `Avocado` |
| Admin password | `1234qweR` |

**Server addressing and roles:**

| Server Role | Hostname | Operating System | IP Address | Default Gateway | Primary DNS | Secondary DNS | Roles |
|-------------|----------|------------------|------------|-----------------|-------------|---------------|-------|
| First domain controller | `samdc1` | Windows Server 2019 | `192.168.116.200/24` | `192.168.116.254` | `192.168.116.200` | `192.168.116.201` | AD DS, DNS, DHCP |
| Second domain controller | `samdc2` | Windows Server 2019 | `192.168.116.201/24` | `192.168.116.254` | `192.168.116.200` | `192.168.116.201` | AD DS, DNS, File Server |
| NAT server | `samnat` | Windows Server 2019 | `192.168.116.254/24` | Not configured on LAN side | `192.168.116.200` | `192.168.116.201` | RRAS, NAT |

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

| Chapter | Description |
|---------|-------------|
| [Network Topology and Lab Planning](docs/01-network-topology-and-lab-planning/README.md) | Define the lab topology, domain name, server roles, addressing plan, operating-system baseline, and virtualization inventory before infrastructure services are installed. |
| [Active Directory Domain Services](docs/02-active-directory-domain-services/README.md) | Build the identity foundation by promoting DC1, adding DC2, creating administrative structures, automating account creation, and validating directory replication. |
| [NAT and Routing with RRAS](docs/03-nat-and-rras-routing/README.md) | Configure SAMNAT as the lab routing server so internal domain systems can reach external networks through a controlled NAT path. |
| [DHCP Services and Failover](docs/04-dhcp-services-and-failover/README.md) | Deploy DHCP services so Windows clients can receive consistent IP addressing, gateway, DNS, and lease settings automatically. |
| [Remote Administration](docs/05-remote-administration/README.md) | Enable controlled Remote Desktop administration for approved administrators and document a lab-only NAT forwarding test. |
| [DNS Services and Name Resolution](docs/06-dns-services-and-name-resolution/README.md) | Configure internal and external DNS behavior, including resolver settings, forwarders, controlled zones, conditional forwarding, stub zones, secondary zones, host records, and round robin records. |
| [Roaming and Mandatory Profiles](docs/07-roaming-and-mandatory-profiles/README.md) | Configure roaming profile storage and convert a profile to mandatory mode so user profile behavior can be controlled across domain workstations. |
| [File Services and Access Control](docs/08-file-services-and-access-control/README.md) | Deploy file services, home folders, DATA share permissions, mapped drives, and FSRM quota controls for domain users and groups. |
| [Group Policy Hardening and Software Deployment](docs/09-group-policy-hardening-and-software-deployment/README.md) | Apply domain-based workstation controls through Group Policy, including user restrictions, removable-storage controls, administrator exceptions, local administrator targeting, and MSI software deployment. |
| [Password Policy and Account Security](docs/10-password-policy-and-account-security/README.md) | Configure a domain-linked password baseline and explain how password policy supports account security in the lab. |
| [Testing, Results, and Recommendations](docs/11-testing-results-and-recommendations/README.md) | Final validation, production notes, limitations, skills, and recommendations |

## Lab Environment Summary

The environment is intentionally small but covers the main moving parts of a Windows domain. DC1 hosts the initial domain services, DC2 provides additional domain-controller and file-server functionality, SAMNAT acts as the lab router, and the Windows 10 client is used to validate user-facing behavior.

| Component | Description |
|----------|-------------|
| Domain | `samueldomain.com` |
| DC1 | Windows Server 2019, AD DS, DNS, DHCP |
| DC2 | Windows Server 2019, additional domain controller, DNS, file server |
| SAMNAT | Windows Server 2019, RRAS NAT router |
| Client | Windows 10 domain-joined workstation |
| Internal network | `192.168.116.0/24` |
| DC1 IP | `192.168.116.200` |
| DC2 IP | `192.168.116.201` |
| NAT/LAN gateway | `192.168.116.254` |
| WAN network | `192.168.68.0/24` lab bridged network |

> The screenshots retain the usernames, domain names, internal addresses, and passwords used inside this isolated training environment.

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

## Key Outcomes

- Built a Windows domain lab with two domain controllers and replicated Active Directory objects.
- Configured RRAS NAT, DHCP services, DHCP failover, DNS forwarding, zones, records, and round robin behavior.
- Validated remote administration, RDP lab forwarding, roaming and mandatory profiles, file shares, mapped drives, and FSRM quota controls.
- Applied Group Policy hardening for standard users, administrator exceptions, removable storage, local administrator targeting, software deployment, and password policy.
- Preserved evidence limits and production recommendations where a screenshot proves configuration but not full production readiness.

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

