# Topology and Lab Environment

This chapter defines the lab topology, domain name, server roles, addressing plan, operating-system baseline, and virtual machines used by the project.

## Topology

The lab starts with a topology and addressing plan because Windows domain services depend on stable DNS, domain-controller addresses, and clear server roles.

The design uses two domain controllers, `SAMNAT` for routing/NAT, and one Windows 10 endpoint for client testing. The topology drawing labels the NAT server as `SRV1`; in the configured lab, that routing server is named `SAMNAT`.

> The topology shows which machines provide identity services, which server routes traffic, where the client sits, and how traffic moves between the domain and the outside network.

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

The environment is small but covers the main Windows domain roles: DC1 for initial domain services, DC2 for redundancy and file services, SAMNAT for routing, and Windows 10 for client validation.

| Component | Description |
|-----------|-------------|
| DC1 | Windows Server 2019, first domain controller, AD DS, DNS, DHCP |
| DC2 | Windows Server 2019, additional domain controller, DNS, file server |
| SAMNAT | Windows Server 2019, RRAS NAT router |
| Client | Windows 10 domain-joined workstation |
| Internal network | `192.168.116.0/24` |
| NAT/LAN gateway | `192.168.116.254` |
| WAN network | `192.168.68.0/24` lab bridged network |

The VMware inventory confirms that DC1, DC2, SAMNAT, and the Windows 10 client were prepared before deployment. Separating roles across VMs keeps the lab closer to a real environment.

![VM Inventory](../../images/02-lab-environment/02-vm-inventory.png)

<p><sub><strong>Screenshot 003 - VM Inventory:</strong> VMware inventory showing the lab virtual machines prepared for the project.</sub></p>

Windows Server 2019 is installed on DC1, DC2, and SAMNAT. During setup, the language is English, the regional format is Hebrew (Israel), and the keyboard layout is US.

> Using the same server OS is not mandatory, but it keeps the lab consistent. Mixed Windows Server versions can also work when roles, domain/forest levels, and applications are compatible.

![Windows Server 2019 Language Setup](../../images/02-lab-environment/03-windows-server-language-setup.png)

<p><sub><strong>Screenshot 004 - Windows Server 2019 Language Setup:</strong> Windows Server 2019 setup with the installation language, regional format, and keyboard layout selected.</sub></p>

## Validation and Summary

Validation comes from the topology diagram, VMware inventory, addressing plan, and Windows Server setup screenshot.

Later AD, RRAS, DHCP, DNS, file-service, and GPO work depends on the hostnames, IP addresses, domain name, and roles defined here.

---

## Project Chapters

| # | Chapter |
|---|---------|
| 0 | [Project Overview](../../README.md) |
| 1 | [Topology and Lab Environment](README.md) |
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
