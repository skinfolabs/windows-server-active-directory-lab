# Topology and Lab Environment

This chapter defines the Windows Server lab before the infrastructure services are installed. It documents the network topology, domain name, server roles, addressing plan, operating-system baseline, and virtual machines that support the rest of the project.

## Topology

The lab starts with a defined topology and addressing plan because Windows domain services depend heavily on predictable DNS, stable domain-controller addresses, and clear server roles. A weak plan at this stage can cause problems later with domain joins, DHCP options, replication, Group Policy processing, and name resolution.

The design uses two domain controllers for identity services, `SAMNAT` for network routing/NAT, and one Windows 10 endpoint to test the user experience from a client perspective. The topology drawing labels the NAT server as `SRV1`; in the configured lab, this same routing server is named `SAMNAT`.

> A topology diagram makes the lab easier to understand before any configuration starts. It shows which machines provide identity services, which server routes traffic, where clients sit, and how traffic is expected to move between the internal domain and the outside network.

![Network Topology](../../images/01-topology/01-network-topology.png)

<p><sub><strong>Screenshot 001 - Network Topology:</strong> Network topology with DC1, DC2, NAT server, Windows 10 client, LAN, and internet path.</sub></p>

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

## Lab Environment

The environment is intentionally small but covers the main moving parts of a Windows domain. DC1 hosts the initial domain services, DC2 provides additional domain-controller and file-server functionality, SAMNAT acts as the lab router, and the Windows 10 client validates user-facing behavior.

| Component | Description |
|-----------|-------------|
| DC1 | Windows Server 2019, first domain controller, AD DS, DNS, DHCP |
| DC2 | Windows Server 2019, additional domain controller, DNS, file server |
| SAMNAT | Windows Server 2019, RRAS NAT router |
| Client | Windows 10 domain-joined workstation |
| Internal network | `192.168.116.0/24` |
| NAT/LAN gateway | `192.168.116.254` |
| WAN network | `192.168.68.0/24` lab bridged network |

The VMware inventory confirms that the required machines were prepared before domain deployment: DC1, DC2, SAMNAT, and the Windows 10 client. Separating roles across different virtual machines makes the lab closer to a real environment than placing every service on one server.

![VM Inventory](../../images/02-lab-environment/02-vm-inventory.png)

<p><sub><strong>Screenshot 003 - VM Inventory:</strong> VMware inventory showing the lab virtual machines prepared for the project.</sub></p>

Windows Server 2019 is installed on all server virtual machines in the lab: DC1, DC2, and SAMNAT. During the initial setup, the installation language is set to English, the regional format is set to Hebrew (Israel), and the keyboard layout is set to US.

> Using the same operating system across all servers is not a strict requirement, but it provides a more consistent platform for Active Directory, DNS, DHCP, file services, and RRAS. A mixed Windows Server environment can also work when the selected versions support the required roles, domain and forest functional levels, and application compatibility.

![Windows Server 2019 Language Setup](../../images/02-lab-environment/03-windows-server-language-setup.png)

<p><sub><strong>Screenshot 004 - Windows Server 2019 Language Setup:</strong> Windows Server 2019 setup with the installation language, regional format, and keyboard layout selected.</sub></p>

## Validation and Summary

The planning evidence is validated by the topology diagram, the VMware inventory, the addressing and role plan, and the Windows Server setup screenshot. These items confirm the intended machines, roles, network path, and operating-system baseline used by the rest of the lab.

This chapter establishes the foundation for the Windows Server lab. The later Active Directory, RRAS, DHCP, DNS, file-service, and Group Policy chapters all depend on the hostnames, IP addresses, domain name, and server roles defined here.

---

## Project Chapters

| # | Chapter | Description |
|---|---------|-------------|
| 0 | [Project Overview](../../README.md) | Main project overview, objectives, tools, and skills |
| 1 | [Topology and Lab Environment](README.md) | Lab topology, addressing, server roles, operating-system baseline, and virtualization inventory |
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
