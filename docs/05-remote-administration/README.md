# Remote Administration

This chapter documents RDP-based remote administration for the lab, including administrator group access and a lab-only NAT forwarding test.


## Technical Context

Remote Desktop is enabled for `Sys_Admins` so administrators can manage servers from the Windows 10 workstation. The lab also demonstrates NAT-forwarded RDP.

Publishing RDP through NAT is treated here as lab-only evidence. In production, this should be replaced with VPN, RD Gateway, MFA, restricted source IPs, and explicit firewall rules.

The lab validates both internal RDP access and an external-side forwarding scenario.

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

> RDP should be limited to approved administrators. Group-based access keeps the permission easier to review and remove.

![Enable Remote Desktop for SysAdmins](../../images/06-remote-management/01-enable-remote-desktop-for-sysadmins.png)

<p><sub><strong>Screenshot 058 - Enable Remote Desktop for SysAdmins:</strong> Remote Desktop enabled for the Sys_Admins group.</sub></p>

---

### Step 02 - Validate RDP from the client

The Windows 10 client connects to a server through Remote Desktop using an authorized administrative user.

> Client-side testing proves that firewall rules, group membership, credentials, and Remote Desktop settings work together.

![RDP Connection as User3](../../images/06-remote-management/04-rdp-connection-as-user3.png)

<p><sub><strong>Screenshot 061 - RDP Connection as User3:</strong> RDP connection from Windows 10 as User 3.</sub></p>

---

### Step 03 - Configure NAT forwarding for RDP lab access

RRAS NAT is configured with a forwarding rule for RDP testing from the external side of the lab.

> Port forwarding maps NAT traffic to an internal machine. It is useful for a lab, but direct RDP exposure increases brute-force and credential-attack risk in production.

![NAT RDP Port Forward Rule](../../images/06-remote-management/06-nat-rdp-port-forward-rule.png)

<p><sub><strong>Screenshot 063 - NAT RDP Port Forward Rule:</strong> NAT port forwarding rule for RDP lab access.</sub></p>

---

### Step 04 - Validate the forwarded RDP connection

The RDP test starts from the physical host, not another lab VM. The host connects to `192.168.68.109:5589`, and RRAS forwards the session internally.

> The host test validates the external-to-internal path: WAN interface, forwarding rule, and translated RDP destination. In production, use VPN or RD Gateway with MFA and source restrictions.

![RDP Port Forward Validation](../../images/06-remote-management/07-rdp-port-forward-validation.png)

<p><sub><strong>Screenshot 064 - RDP Port Forward Validation:</strong> RDP connection initiated from the physical host through SAMNAT's forwarded lab port.</sub></p>

---

## Validation and Summary


Validation confirms Remote Desktop settings, `Sys_Admins` membership, client RDP access, SAMNAT WAN addressing, NAT forwarding, and a connection test from the physical host.


This chapter demonstrates domain-based RDP administration and a lab-only forwarding scenario, with production guidance for safer remote access.

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
