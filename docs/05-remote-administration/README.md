# Remote Administration

This chapter covers Remote Administration in the Windows Server infrastructure lab. It explains what was configured, why the configuration matters, and which evidence validates the result.


## Technical Context

Remote Desktop is enabled for the `Sys_Admins` group so administrators can manage servers from the Windows 10 workstation. The lab also demonstrates RDP access through a NAT forwarding rule.

Publishing RDP through NAT is treated here as lab-only evidence. In production, this should be replaced with VPN, RD Gateway, MFA, restricted source IPs, and explicit firewall rules.

This section is included because infrastructure administration is not only about installing services; administrators also need controlled, auditable ways to access servers. The lab validates both internal RDP access and an external-side forwarding scenario.

**Implemented controls:**

- Enabled Remote Desktop access for the administrator group.
- Validated RDP access from the Windows 10 workstation.
- Configured a lab-only NAT forwarding rule for RDP.
- Confirmed external-side RDP connectivity through SAMNAT.
- Documented production security considerations for exposed RDP.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| RDP | Remote Desktop Protocol, used for graphical remote administration of Windows systems. |
| NAT forwarding | A lab mapping that forwards inbound traffic from SAMNAT to an internal RDP destination. |
| Administrative group | A security group used to grant remote access to approved administrators instead of individual users. |
| RD Gateway | A production-friendly remote-access component that avoids exposing direct RDP to the internet. |

---

## Detailed Walkthrough

### Step 01 - Enable RDP for administrators

The server is configured to allow RDP access for the administrative group.

> RDP allows administrators to manage servers remotely, but it should be limited to approved administrative users. Granting access through a group keeps the permission easier to review and remove.

![Enable Remote Desktop for SysAdmins](../../images/06-remote-management/01-enable-remote-desktop-for-sysadmins.png)

<p><sub><strong>Screenshot 058 - Enable Remote Desktop for SysAdmins:</strong> Remote Desktop enabled for the Sys_Admins group.</sub></p>

---

### Step 02 - Validate RDP from the client

The Windows 10 client connects to a server through Remote Desktop using an authorized administrative user.

> Testing from the client side proves that firewall rules, group membership, credentials, and Remote Desktop settings all work together. It also confirms that access is usable for real administration, not only enabled in a settings window.

![RDP Connection as User3](../../images/06-remote-management/04-rdp-connection-as-user3.png)

<p><sub><strong>Screenshot 061 - RDP Connection as User3:</strong> RDP connection from Windows 10 as User 3.</sub></p>

---

### Step 03 - Configure NAT forwarding for RDP lab access

RRAS NAT is configured with a forwarding rule for RDP testing from the external side of the lab.

> Port forwarding maps traffic arriving on the NAT server to an internal machine. This is useful for a lab demonstration, but exposing RDP directly is risky in production because it increases brute-force and credential-attack exposure.

![NAT RDP Port Forward Rule](../../images/06-remote-management/06-nat-rdp-port-forward-rule.png)

<p><sub><strong>Screenshot 063 - NAT RDP Port Forward Rule:</strong> NAT port forwarding rule for RDP lab access.</sub></p>

---

### Step 04 - Validate the forwarded RDP connection

The RDP connection is initiated from the physical host computer rather than from another virtual machine inside the lab. The host connects to SAMNAT's WAN address at `192.168.68.109:5589`, and RRAS forwards the session to the internal RDP destination.

> Testing from the physical computer validates the complete external-to-internal path: the connection reaches the WAN interface, matches the forwarding rule, and is translated to the internal system. Direct RDP forwarding remains a lab-only design; production access should use VPN or RD Gateway with MFA and source restrictions.

![RDP Port Forward Validation](../../images/06-remote-management/07-rdp-port-forward-validation.png)

<p><sub><strong>Screenshot 064 - RDP Port Forward Validation:</strong> RDP connection initiated from the physical host through SAMNAT's forwarded lab port.</sub></p>

---

## Validation and Summary


Validation is based on Remote Desktop settings, Sys_Admins group membership, a successful RDP session from the client, SAMNAT WAN addressing, a NAT forwarding rule, and the forwarded RDP connection test from the physical host.


This chapter demonstrates domain-based remote administration and a lab-only external forwarding scenario. It also documents why direct RDP exposure should be replaced by VPN, RD Gateway, MFA, and strict access controls in production.

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
