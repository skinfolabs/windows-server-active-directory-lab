# DHCP Services and Failover

This chapter covers DHCP Services and Failover in the Windows Server infrastructure lab. It explains what was configured, why the configuration matters, and which evidence validates the result.


## Technical Context

DHCP is installed on DC1 to automatically provide IP configuration to client systems. Instead of manually configuring every workstation, DHCP distributes the address, subnet mask, gateway, DNS servers, and lease information from a central service.

The lab also configures a DHCP failover relationship with DC2. This demonstrates service continuity: if one DHCP server is unavailable, the partner server can continue supporting client address assignment.

From a security and operations point of view, DHCP has to be predictable. Wrong gateway or DNS options can break authentication, redirect traffic, or make clients use the wrong name-resolution path, so the scope, exclusions, and authorization are treated as controls rather than simple setup steps.

**Implemented controls:**

- Installed and authorized the DHCP Server role.
- Created a controlled DHCP scope for client addressing.
- Configured exclusions for reserved infrastructure addresses.
- Assigned gateway and DNS options to clients.
- Built a DHCP failover relationship with DC2.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| DHCP | Dynamic Host Configuration Protocol, which automatically gives clients IP settings. |
| Scope | The range of IP addresses a DHCP server may lease to clients. |
| Exclusion | Addresses inside the scope that DHCP must not lease because they are reserved for infrastructure. |
| DHCP options | Extra settings such as gateway and DNS server delivered with the lease. |
| DHCP failover | A relationship between two DHCP servers that shares lease state for availability. |

---

## Detailed Walkthrough

### Step 01 - Install the DHCP Server role

The DHCP Server role is selected in Server Manager. This prepares DC1 to distribute IP configuration to client systems.

> DHCP removes the need to manually configure every client with an IP address, gateway, and DNS server. In a domain, this is especially important because clients must receive the correct internal DNS settings to find Active Directory.

![Select DHCP Server Role](../../images/05-dhcp/02-select-dhcp-server-role.png)

<p><sub><strong>Screenshot 042 - Select DHCP Server Role:</strong> DHCP Server role selection.</sub></p>

---

### Step 02 - Authorize DHCP in Active Directory

The DHCP server is authorized so it can issue leases in the AD domain environment. Authorization helps prevent an unmanaged DHCP server from silently handing out incorrect network settings inside the domain.

> In Active Directory environments, DHCP authorization is a protection mechanism against rogue DHCP servers. A rogue server can give clients the wrong gateway or DNS server, which can cause outages or enable traffic redirection.

![DHCP Authorization](../../images/05-dhcp/04-dhcp-authorization.png)

<p><sub><strong>Screenshot 044 - DHCP Authorization:</strong> DHCP authorization using domain credentials.</sub></p>

---

### Step 03 - Create the DHCP scope

The DHCP scope defines the client address range. This lab uses a controlled range inside the internal subnet.

> A scope tells DHCP which addresses it is allowed to lease. Defining a scope prevents random addressing and keeps client IP assignments inside the planned network range.

![DHCP Scope Address Range](../../images/05-dhcp/07-dhcp-scope-address-range.png)

<p><sub><strong>Screenshot 047 - DHCP Scope Address Range:</strong> DHCP scope address range configuration.</sub></p>

---

### Step 04 - Add exclusions

The first addresses in the range are excluded so they can remain reserved for static infrastructure or other planned use.

> Exclusions prevent DHCP from leasing addresses that should stay reserved for servers, routers, printers, or future infrastructure. This avoids IP conflicts between manually configured systems and DHCP clients.

![DHCP Exclusion Range](../../images/05-dhcp/08-dhcp-exclusion-range.png)

<p><sub><strong>Screenshot 048 - DHCP Exclusion Range:</strong> DHCP exclusion range configuration.</sub></p>

---

### Step 05 - Configure DHCP options

The scope provides gateway and DNS settings to clients. The gateway points to SAMNAT and DNS points to the domain DNS servers.

> DHCP options are just as important as the IP address itself. The default gateway tells clients how to leave the subnet, and DNS options tell them where to resolve domain names and locate domain controllers.

![DNS Server Option](../../images/05-dhcp/12-dns-server-option.png)

<p><sub><strong>Screenshot 052 - DNS Server Option:</strong> DHCP DNS server option for domain clients.</sub></p>

---

### Step 06 - Validate automatic client configuration

The Windows 10 client receives its IP configuration automatically from DHCP. This validates the DHCP scope and options.

> Client validation proves that the DHCP server is not only configured, but actually usable from the endpoint side. It also confirms that the client receives the intended DNS and gateway values.

![Windows 10 DHCP Address Validation](../../images/05-dhcp/13-win10-dhcp-address-validation.png)

<p><sub><strong>Screenshot 053 - Windows 10 DHCP Address Validation:</strong> Windows 10 receiving automatic DHCP address configuration.</sub></p>

---

### Step 07 - Configure DHCP failover

The lab configures a DHCP failover relationship with DC2. This is more accurately described as DHCP failover, not a failover cluster.

> DHCP failover lets two DHCP servers share responsibility for leases. If one server is unavailable, the other can continue leasing addresses, which improves availability for client network access.

![DHCP Failover Relationship](../../images/05-dhcp/17-dhcp-failover-relationship.png)

<p><sub><strong>Screenshot 057 - DHCP Failover Relationship:</strong> DHCP failover relationship settings.</sub></p>

---

## Validation and Summary


Validation is based on DHCP role installation, authorization, scope configuration, option screens, client IP assignment, network connectivity, and DHCP failover partner configuration.


This chapter automates client addressing and adds DHCP failover. The Windows 10 client receives the expected network settings, and DC2 is configured as a failover partner for address-service continuity.

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
| 11 | [Final Summary](../11-final-summary/README.md) | Validation summary, production recommendations, skills, and project closure |
