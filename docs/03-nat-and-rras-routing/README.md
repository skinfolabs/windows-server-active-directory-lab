# NAT and Routing with RRAS

This chapter covers NAT and Routing with RRAS in the Windows Server infrastructure lab. It explains what was configured, why the configuration matters, and which evidence validates the result.

---

## Purpose

Configure SAMNAT as the lab routing server so internal domain systems can reach external networks through a controlled NAT path.

## Technical Context

SAMNAT is configured with an internal LAN interface and an external bridged interface. RRAS provides NAT so internal lab systems can reach external networks without assigning each internal host direct external access.

PAT is documented here as a NAT translation method, not a standalone protocol. It allows multiple internal systems to share one external address by translating sessions with unique port mappings.

The purpose of this section is to make the isolated lab network usable for validation tasks such as DNS forwarding tests, updates, and external connectivity checks while keeping the internal subnet controlled.

**Implemented controls:**

- Renamed SAMWINPC1 and SAMNAT and joined both systems to `samueldomain.com`.
- Configured separate LAN and WAN interfaces on SAMNAT.
- Installed the Remote Access role for RRAS.
- Enabled NAT and LAN routing.
- Marked the WAN adapter as the public NAT interface.
- Validated outbound connectivity from internal systems.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| RRAS | Routing and Remote Access Service, the Windows role used here for lab routing and NAT. |
| NAT | Network Address Translation, which lets private internal hosts communicate through a translated external path. |
| PAT | Port Address Translation, where multiple internal sessions share one outside address by using unique ports. |
| LAN interface | The internal adapter facing the domain subnet. |
| WAN interface | The adapter facing the external or bridged network path. |

## Steps Covered

| Step | Description |
|------|-------------|
| Name and join SAMWINPC1 and SAMNAT to the domain | The Windows 10 client is renamed SAMWINPC1 and joined to samueldomain.com . |
| Configure SAMNAT network interfaces | SAMNAT has a LAN interface for the internal 192.168.116.0/24 network and a WAN interface connected to the bridged external network. |
| Install the Remote Access role | The Remote Access role is installed to provide Routing and Remote Access Service functionality. |
| Enable NAT and LAN routing | RRAS is configured with NAT and LAN routing. |
| Mark the WAN interface as public | The WAN interface is selected as the public interface connected to the external network. |
| Validate internet connectivity from DC1 | After NAT is configured, DC1 can reach an external DNS address. |

---

## Detailed Walkthrough

### Step 01 - Name and join SAMWINPC1 and SAMNAT to the domain

The Windows 10 client is renamed `SAMWINPC1` and joined to `samueldomain.com`. A clear client hostname makes the workstation easy to identify in Active Directory, DNS, DHCP leases, Group Policy results, and administrative logs. Domain membership allows the client to authenticate domain users and receive centrally managed settings.

![SAMWINPC1 Domain Membership](../../images/04-nat-routing/01-samwinpc1-domain-membership.png)

<p><sub><strong>Screenshot 028 - SAMWINPC1 Domain Membership:</strong> SAMWINPC1 configured with its computer name and joined to the samueldomain.com domain.</sub></p>

The routing server is renamed `SAMNAT` and joined to the same domain. After the domain change, Windows reports the full computer name as `SAMNAT.SamuelDomain.com`. `SAMNAT` is the short hostname, while `SAMNAT.SamuelDomain.com` is the fully qualified domain name that identifies the server inside the domain DNS namespace.

> RRAS and NAT can operate on a standalone Windows Server, so domain membership is not a technical requirement for packet routing. In this lab, joining SAMNAT to the domain provides centralized administration, domain authentication, DNS registration, and consistent access to management tools.

![SAMNAT Domain Membership](../../images/04-nat-routing/02-samnat-domain-membership.png)

<p><sub><strong>Screenshot 029 - SAMNAT Domain Membership:</strong> SAMNAT joined to SamuelDomain.com with the full computer name SAMNAT.SamuelDomain.com.</sub></p>

---

### Step 02 - Configure SAMNAT network interfaces

SAMNAT has a LAN interface for the internal `192.168.116.0/24` network and a WAN interface connected to the bridged external network.

> A NAT router needs at least two sides: an inside network and an outside network. The LAN adapter faces the private domain network, while the WAN adapter provides the path toward the external network.

![SAMNAT NIC Setup](../../images/04-nat-routing/03-samnat-domain-join-and-nic-setup.png)

<p><sub><strong>Screenshot 030 - SAMNAT NIC Setup:</strong> SAMNAT domain membership and network interface setup.</sub></p>

---

### Step 03 - Install the Remote Access role

The Remote Access role is installed to provide Routing and Remote Access Service functionality.

> RRAS is the Windows Server feature that can provide routing, NAT, and remote access capabilities. In this lab it is used as a router/NAT service so internal machines do not need direct external network exposure.

![Remote Access Role Selection](../../images/04-nat-routing/07-remote-access-role-selection.png)

<p><sub><strong>Screenshot 034 - Remote Access Role Selection:</strong> Remote Access role selected for NAT/RRAS.</sub></p>

---

### Step 04 - Enable NAT and LAN routing

RRAS is configured with NAT and LAN routing. This allows internal hosts to send traffic through SAMNAT.

> NAT translates private internal addresses into an address that can communicate externally. PAT, often called NAT overload, also tracks sessions by port number so multiple internal machines can share the same outside path at the same time.

![NAT and LAN Routing Selection](../../images/04-nat-routing/09-nat-and-lan-routing-selection.png)

<p><sub><strong>Screenshot 036 - NAT and LAN Routing Selection:</strong> RRAS custom configuration with NAT and LAN routing.</sub></p>

---

### Step 05 - Mark the WAN interface as public

The WAN interface is selected as the public interface connected to the external network. NAT is enabled on this interface.

> RRAS must know which adapter is internal and which one is external. Marking the wrong adapter as public can break routing or accidentally expose the wrong side of the network.

![NAT Public Interface Configuration](../../images/04-nat-routing/10-nat-public-interface-configuration.png)

<p><sub><strong>Screenshot 037 - NAT Public Interface Configuration:</strong> NAT public interface configuration.</sub></p>

---

### Step 06 - Validate internet connectivity from DC1

After NAT is configured, DC1 can reach an external DNS address. This proves that internal systems can route outbound traffic through SAMNAT.

> A connectivity test confirms that the design works end to end: DC1 sends traffic to its gateway, SAMNAT translates and routes it, and the response returns correctly. This validation is needed before later DNS forwarding and update-related tests.

![DC1 Internet Connectivity Test](../../images/04-nat-routing/11-dc1-internet-connectivity-test.png)

<p><sub><strong>Screenshot 038 - DC1 Internet Connectivity Test:</strong> DC1 ping test to public DNS through NAT.</sub></p>

---

## Validation

The routing configuration is validated by RRAS role selection, NAT and LAN routing configuration, public interface selection, and ping tests from DC1, DC2, and the Windows 10 client.

## Chapter Summary

This chapter provides outbound lab connectivity through SAMNAT. That path supports later external DNS forwarding tests, client connectivity checks, and lab-only remote-access validation.

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

