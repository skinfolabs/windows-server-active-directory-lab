# DNS Services and Name Resolution

This chapter covers DNS Services and Name Resolution in the Windows Server infrastructure lab. It explains what was configured, why the configuration matters, and which evidence validates the result.

---

## Purpose

Configure internal and external DNS behavior, including resolver settings, forwarders, controlled zones, conditional forwarding, stub zones, secondary zones, host records, and round robin records.

## Technical Context

DNS supports the domain and provides internal name resolution, external forwarding, controlled domain blocking, zone management, stub zones, and round robin behavior. In Active Directory, DNS is critical because clients use it to locate domain controllers and domain services.

This section demonstrates both internal DNS administration and external resolution behavior. The configuration includes forwarders, custom zones, conditional forwarding, a stub zone, secondary zone behavior, host records, and round robin testing.

DNS is treated here as both an infrastructure service and a security control. If DNS is wrong, domain logon, Kerberos, Group Policy, software deployment, and file access can all fail. If DNS is intentionally controlled, it can also be used to steer or block name resolution in a lab-safe way.

**Implemented controls:**

- Verified domain DNS settings on client and server systems.
- Configured external DNS forwarding.
- Created custom zones for controlled name-resolution behavior.
- Configured conditional forwarding and stub zone examples.
- Built primary and secondary zone behavior.
- Validated host records and round robin DNS responses.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| Forwarder | A DNS server that receives unresolved queries from the internal DNS server. |
| Conditional forwarder | A domain-specific forwarding rule for one namespace, such as a partner or separate forest. |
| Stub zone | A small zone copy containing SOA, NS, and glue records used to locate authoritative name servers. |
| Secondary zone | A read-only DNS zone copy transferred from a primary server. |
| Round robin | DNS behavior that rotates multiple IP addresses registered under the same hostname. |

## Steps Covered

| Step | Description |
|------|-------------|
| Confirm DNS client settings | Domain systems are configured to use the internal domain DNS servers. |
| Configure external DNS forwarding | External forwarding is configured so internal DNS servers can forward unresolved queries to an upstream DNS provider. |
| Create a controlled zone for Facebook blocking | The lab creates a DNS zone for facebook.com and adds a controlled host record that prevents normal resolution. |
| Validate the Facebook resolution result | The client validation shows that the configured DNS response prevents normal access to the target domain. |
| Discover Google name servers | nslookup is run from the Windows 10 client with the query type set to NS . |
| Configure a Google conditional forwarder | DC1 is configured with a conditional forwarder for google.com that sends matching queries to ns1.google.com at 216.239.32.10 . |
| Configure a Yahoo stub zone | A stub zone for yahoo.com is created from the master server ns2.yahoo.com at 68.142.255.16 . |
| Create a secondary DNS zone | The lab creates a primary zone on DC1 and a secondary zone on DC2. |
| Create and validate a New Host record | The New Host action in DNS Manager creates an A record that maps a readable host name to an IPv4 address. |
| Configure and validate DNS round robin | DNS round robin is a basic load distribution method that places several IP addresses behind one host name. |

---

## Detailed Walkthrough

### Step 01 - Confirm DNS client settings

Domain systems are configured to use the internal domain DNS servers. This is required for domain join, authentication, and service discovery.

> Active Directory depends on DNS service records. If a client uses an external DNS server instead of the domain DNS server, it may resolve internet names but still fail to find domain controllers, logon services, or Group Policy paths.

![Windows 10 DNS Client Settings](../../images/07-dns/05-win10-dns-client-settings.png)

<p><sub><strong>Screenshot 069 - Windows 10 DNS Client Settings:</strong> Windows 10 DNS client settings pointing to the domain DNS server.</sub></p>

---

### Step 02 - Configure external DNS forwarding

External forwarding is configured so internal DNS servers can forward unresolved queries to an upstream DNS provider.

> Internal DNS should answer domain-related queries itself, but it still needs a path for internet names. A forwarder lets the domain DNS server keep control of internal resolution while sending unknown external queries to a trusted upstream resolver.

![DC1 External Forwarder](../../images/07-dns/08-dc1-external-forwarder.png)

<p><sub><strong>Screenshot 072 - DC1 External Forwarder:</strong> DC1 external DNS forwarder configured for recursive resolution.</sub></p>

---

### Step 03 - Create a controlled zone for Facebook blocking

The lab creates a DNS zone for `facebook.com` and adds a controlled host record that prevents normal resolution. This demonstrates DNS-based name-resolution control in a lab context.

> DNS can be used to influence where clients go when they request a name. In this lab, a controlled zone demonstrates how name resolution can block or redirect access, which is useful for understanding both defensive filtering and the risk of malicious DNS manipulation.

![Facebook Zone Name](../../images/07-dns/11-facebook-zone-name.png)

<p><sub><strong>Screenshot 075 - Facebook Zone Name:</strong> Facebook.com zone name configuration.</sub></p>

---

### Step 04 - Validate the Facebook resolution result

The client validation shows that the configured DNS response prevents normal access to the target domain.

> A DNS control is only useful if the endpoint receives the expected answer. Testing from the client confirms that the DNS zone is actually affecting user traffic, not just existing in DNS Manager.

![Facebook Resolution Block Validation](../../images/07-dns/13-facebook-resolution-block-validation.png)

<p><sub><strong>Screenshot 077 - Facebook Resolution Block Validation:</strong> Client validation showing blocked or unreachable Facebook resolution.</sub></p>

---

### Step 05 - Discover Google name servers

`nslookup` is run from the Windows 10 client with the query type set to `NS`. The client sends the request to DC1 at `192.168.116.200` and receives the authoritative Google name servers, including `ns1.google.com`, `ns2.google.com`, `ns3.google.com`, and `ns4.google.com`.

> The successful response confirms that the client is using DC1 as its DNS server and that DC1 can resolve external DNS data. The `Server: Unknown` label only indicates that a reverse PTR record is not available for the DNS server address; it does not mean the lookup failed.

![Google nslookup Output](../../images/07-dns/14-google-nslookup-output.png)

<p><sub><strong>Screenshot 078 - Google nslookup Output:</strong> `nslookup` output for Google DNS records.</sub></p>

---

### Step 06 - Configure a Google conditional forwarder

DC1 is configured with a conditional forwarder for `google.com` that sends matching queries to `ns1.google.com` at `216.239.32.10`. Queries for other domains continue to use the server's normal DNS resolution path.

> Conditional forwarding creates a domain-specific resolution path. It is commonly used when an organization must resolve a partner domain, a private namespace, or another Active Directory forest through designated DNS servers without changing resolution for every other domain. Google is used here to demonstrate the mechanism in the lab.

![Google Conditional Forwarder](../../images/07-dns/15-google-conditional-forwarder.png)

<p><sub><strong>Screenshot 079 - Google Conditional Forwarder:</strong> Google conditional forwarder configured with a master server.</sub></p>

---

### Step 07 - Configure a Yahoo stub zone

A stub zone for `yahoo.com` is created from the master server `ns2.yahoo.com` at `68.142.255.16`. DC1 uses that server to obtain the zone's SOA record, NS records, and the address records needed to locate Yahoo's authoritative DNS servers.

> Unlike a conditional forwarder, which sends matching queries to a fixed DNS server, a stub zone maintains a small, refreshable list of authoritative servers for the target zone. This is useful when DNS should follow the authoritative server set as it changes without storing a complete copy of the external zone.

![Yahoo Stub Zone Master Servers](../../images/07-dns/17-yahoo-stub-zone-master-servers.png)

<p><sub><strong>Screenshot 081 - Yahoo Stub Zone Master Servers:</strong> Stub zone master servers for Yahoo.</sub></p>

---

### Step 08 - Create a secondary DNS zone

The lab creates a primary zone on DC1 and a secondary zone on DC2. The secondary zone receives data from the primary DNS server.

> A secondary zone is a read-only copy of a DNS zone transferred from a primary server. This improves availability for name resolution and demonstrates how DNS data can be replicated between DNS servers.

![Secondary Zone Master IP](../../images/07-dns/21-secondary-zone-master-ip.png)

<p><sub><strong>Screenshot 085 - Secondary Zone Master IP:</strong> Primary DNS server IP used for secondary zone transfer.</sub></p>

---

### Step 09 - Create and validate a New Host record

The **New Host** action in DNS Manager creates an `A` record that maps a readable host name to an IPv4 address. In this step, the name `mail` is mapped to DC1 at `192.168.116.200`, creating the fully qualified domain name `mail.samueldomain.com`.

> New Host records allow users and applications to reach systems by name instead of remembering IP addresses. They can identify web servers, mail services, file servers, application endpoints, printers, and other network devices. The `ping -a mail` test from the Windows 10 client confirms that the new name resolves to the intended address.

![Mail A Record Created](../../images/07-dns/23-mail-a-record-created.png)

<p><sub><strong>Screenshot 087 - Mail A Record Created:</strong> Mail A record created for lab name-resolution validation.</sub></p>

After the record is created, PC1 resolves `mail.samueldomain.com` to `192.168.116.200` and receives replies from DC1. This confirms that the New Host record is available to domain clients through the internal DNS service.

![Mail Host Record Validation](../../images/07-dns/24-mail-record-ping-validation.png)

<p><sub><strong>Screenshot 088 - Mail Host Record Validation:</strong> PC1 resolving and reaching the new `mail.samueldomain.com` host record.</sub></p>

---

### Step 10 - Configure and validate DNS round robin

DNS round robin is a basic load-distribution method that places several IP addresses behind one host name. DC1 rotates the order of those addresses in its responses, helping spread client requests across multiple equivalent servers instead of directing every request to one system.

First, three `A` records are created with the same test hostname and different Google server addresses. The **Enable round robin** option is then enabled in the advanced properties of the DC1 DNS service. Finally, repeated `nslookup` queries are run from PC1 to confirm that the returned address order changes.

> Round robin can reduce the concentration of traffic on one server and improve basic service distribution. It is not a full load balancer because DNS does not check whether a server is healthy, and client-side DNS caching can influence which address is selected.

![Round Robin Host Records](../../images/07-dns/25-round-robin-host-records.png)

<p><sub><strong>Screenshot 089 - Round Robin Host Records:</strong> Three host records using the same name and different IP addresses for round robin.</sub></p>

![Enable DNS Round Robin](../../images/07-dns/26-round-robin-enabled.png)

<p><sub><strong>Screenshot 090 - Enable DNS Round Robin:</strong> Round robin enabled in the advanced properties of the DC1 DNS service.</sub></p>

![Round Robin Validation](../../images/07-dns/27-round-robin-validation.png)

<p><sub><strong>Screenshot 091 - Round Robin Validation:</strong> Repeated `nslookup` queries from PC1 showing the returned addresses in a different order.</sub></p>

---

## Validation

Validation is based on DNS client settings, forwarder configuration, DNS zone screens, nslookup output, ping tests, secondary-zone visibility, and round robin query behavior from a client.

## Chapter Summary

This chapter shows how DNS supports domain services and name-resolution control. It validates internal host records, forwarding behavior, secondary zones, stub zones, and basic DNS round robin behavior.

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

