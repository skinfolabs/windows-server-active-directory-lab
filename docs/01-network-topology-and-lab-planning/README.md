# Network Topology and Lab Planning

This chapter covers Network Topology and Lab Planning in the Windows Server infrastructure lab. It explains what was configured, why the configuration matters, and which evidence validates the result.

---

## Purpose

Define the lab topology, domain name, server roles, addressing plan, operating-system baseline, and virtualization inventory before infrastructure services are installed.

## Technical Context

The lab starts with a defined topology and addressing plan. This stage matters because Windows domain services depend heavily on predictable DNS, stable domain-controller addresses, and clear server roles. A weak plan at this point can cause problems later with domain joins, DHCP options, replication, and name resolution.

The design uses two domain controllers for identity services, SAMNAT for network routing/NAT, and one Windows 10 endpoint to test the user experience from a client perspective.

**Implemented controls:**

- Defined the logical topology and traffic path.
- Documented server roles before installation.
- Planned static IP addressing for infrastructure systems.
- Prepared the VMware virtual machines required for the lab.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| Domain | The Active Directory namespace used for centralized authentication and management. |
| Domain controller | A server that stores and serves the directory database for logons, DNS records, and policy processing. |
| Static IP | A fixed address used for infrastructure servers so clients can consistently locate core services. |
| Default gateway | The router address used when a host sends traffic outside its local subnet. |
| DNS server | The resolver clients use to locate domain controllers, services, and external names. |

## Steps Covered

| Step | Description |
|------|-------------|
| Network topology | This diagram shows the relationship between DC1, DC2, SAMNAT, the internal LAN, the Windows 10 client, and the external internet path. |
| Lab addressing and role plan | Before installation, the lab defines hostnames, IP addresses, gateway settings, DNS settings, and server roles. |
| Virtual machine inventory | The VMware inventory confirms that the required machines were prepared before domain deployment: DC1, DC2, SAMNAT, and the Windows 10 client. |
| Prepare the Windows Server 2019 installation | Windows Server 2019 is installed on all server virtual machines in the lab: DC1, DC2, and SAMNAT. |

---

## Detailed Walkthrough

### Step 01 - Network topology

This diagram shows the relationship between DC1, DC2, SAMNAT, the internal LAN, the Windows 10 client, and the external internet path. It is the baseline architecture used throughout the project.

> A topology diagram makes the lab easier to understand before any configuration starts. It shows which machines provide identity services, which server routes traffic, where clients sit, and how traffic is expected to move between the internal domain and the outside network.

![Network Topology](../../images/01-topology/01-network-topology.png)

<p><sub><strong>Screenshot 001 - Network Topology:</strong> Network topology with DC1, DC2, NAT server, Win10 client, LAN and internet path.</sub></p>

The topology drawing labels the NAT server as `SRV1`. In the configured lab, this same routing server is named `SAMNAT`.

---

### Step 02 - Lab addressing and role plan

Before installation, the lab defines hostnames, IP addresses, gateway settings, DNS settings, and server roles. This prevents later conflicts and makes the implementation easier to validate. The table below presents the planning data as a cleaner technical reference.

> Domain controllers, DNS servers, DHCP scopes, and routing depend on stable addressing. If a domain controller IP changes unexpectedly, clients may fail to locate the domain, join the domain, apply Group Policy, or resolve internal names correctly.

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

---

### Step 03 - Virtual machine inventory

The VMware inventory confirms that the required machines were prepared before domain deployment: DC1, DC2, SAMNAT, and the Windows 10 client.

> The inventory proves that the lab has the correct systems before services are installed. Separating roles across different virtual machines also makes the design closer to a real environment than installing every service on one server.

![VM Inventory](../../images/02-lab-environment/02-vm-inventory.png)

<p><sub><strong>Screenshot 003 - VM Inventory:</strong> VMware inventory showing the lab virtual machines.</sub></p>

---

### Step 04 - Prepare the Windows Server 2019 installation

Windows Server 2019 is installed on all server virtual machines in the lab: DC1, DC2, and SAMNAT. During the initial setup, the installation language is set to English, the regional format is set to Hebrew (Israel), and the keyboard layout is set to US.

> Using the same operating system across all servers is not a strict requirement, but it provides a more consistent platform for Active Directory, DNS, DHCP, file services, and RRAS. A mixed Windows Server environment can also work when the selected versions support the required roles, domain and forest functional levels, and application compatibility.

![Windows Server 2019 Language Setup](../../images/02-lab-environment/03-windows-server-language-setup.png)

<p><sub><strong>Screenshot 004 - Windows Server 2019 Language Setup:</strong> Windows Server 2019 setup with the installation language, regional format, and keyboard layout selected.</sub></p>

---

## Validation

The planning evidence is validated by the topology diagram, the VMware inventory, the addressing and role plan, and the Windows Server setup screenshot. These items confirm the intended machines, roles, network path, and operating-system baseline used by the rest of the lab.

## Chapter Summary

This chapter establishes the foundation for the Windows Server lab. The later Active Directory, RRAS, DHCP, DNS, file-service, and Group Policy chapters all depend on the hostnames, IP addresses, domain name, and server roles defined here.

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
