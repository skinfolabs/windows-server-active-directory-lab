# DHCP Services and Failover

This chapter documents DHCP deployment, scope design, client lease validation, and DHCP failover between the Windows Server systems.


## Technical Context

DHCP is installed on DC1 to centrally provide client IP address, subnet mask, gateway, DNS, and lease settings.

DC2 is configured as a DHCP failover partner so client address assignment can continue if one DHCP server is unavailable.

DHCP must be predictable: wrong gateway or DNS options can break authentication, redirect traffic, or send clients to the wrong name-resolution path.

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

> DHCP removes manual client addressing. In a domain, correct DNS options are critical because clients must find Active Directory.

![Select DHCP Server Role](../../images/05-dhcp/02-select-dhcp-server-role.png)

<p><sub><strong>Screenshot 042 - Select DHCP Server Role:</strong> DHCP Server role selection.</sub></p>

---

### Step 02 - Authorize DHCP in Active Directory

The DHCP server is authorized in AD before issuing leases, helping prevent unmanaged DHCP from distributing incorrect settings.

> DHCP authorization protects against rogue DHCP servers that could hand out a wrong gateway or DNS server.

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

> Exclusions keep reserved infrastructure addresses out of the DHCP lease pool and prevent IP conflicts.

![DHCP Exclusion Range](../../images/05-dhcp/08-dhcp-exclusion-range.png)

<p><sub><strong>Screenshot 048 - DHCP Exclusion Range:</strong> DHCP exclusion range configuration.</sub></p>

---

### Step 05 - Configure DHCP options

The scope provides gateway and DNS settings to clients. The gateway points to SAMNAT and DNS points to the domain DNS servers.

> DHCP options are as important as the IP address: gateway controls routing, and DNS controls domain name resolution.

![DNS Server Option](../../images/05-dhcp/12-dns-server-option.png)

<p><sub><strong>Screenshot 052 - DNS Server Option:</strong> DHCP DNS server option for domain clients.</sub></p>

---

### Step 06 - Validate automatic client configuration

The Windows 10 client receives its IP configuration automatically from DHCP. This validates the DHCP scope and options.

> Client validation proves DHCP works from the endpoint side and delivers the intended DNS and gateway values.

![Windows 10 DHCP Address Validation](../../images/05-dhcp/13-win10-dhcp-address-validation.png)

<p><sub><strong>Screenshot 053 - Windows 10 DHCP Address Validation:</strong> Windows 10 receiving automatic DHCP address configuration.</sub></p>

---

### Step 07 - Configure DHCP failover

The lab configures DHCP failover with DC2. This is DHCP service continuity, not a Windows failover cluster.

> DHCP failover lets two servers share lease responsibility so the partner can continue leasing addresses during an outage.

![DHCP Failover Relationship](../../images/05-dhcp/17-dhcp-failover-relationship.png)

<p><sub><strong>Screenshot 057 - DHCP Failover Relationship:</strong> DHCP failover relationship settings.</sub></p>

---

## Validation and Summary


Validation confirms DHCP role installation, authorization, scope options, client addressing, connectivity, and failover partner configuration.


This chapter automates client addressing and adds DHCP failover. The client receives the expected settings, and DC2 is configured for address-service continuity.

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
| 11 | [Final Summary](../11-final-summary/README.md) |
