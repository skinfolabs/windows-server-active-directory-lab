# DNS Services and Name Resolution

This chapter documents DNS administration for the domain: internal resolution, external forwarding, controlled zones, stub zones, secondary zones, host records, and round robin testing.


## Technical Context

DNS supports domain logon and name resolution. In Active Directory, clients rely on DNS records to locate domain controllers, Kerberos services, Group Policy paths, and internal resources.

The configuration covers both internal domain resolution and external lookup behavior.

DNS is also used as a lab-safe control point for steering or blocking name resolution.

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

---

## Detailed Walkthrough

### Step 01 - Confirm DNS client settings

Domain systems are configured to use the internal domain DNS servers. This is required for domain join, authentication, and service discovery.

> If a domain client uses external DNS instead of domain DNS, internet names may work while logon services, Group Policy, and domain-controller discovery fail.

![Windows 10 DNS Client Settings](../../images/07-dns/05-win10-dns-client-settings.png)

<p><sub><strong>Screenshot 069 - Windows 10 DNS Client Settings:</strong> Windows 10 DNS client settings pointing to the domain DNS server.</sub></p>

---

### Step 02 - Configure external DNS forwarding

External forwarding sends unresolved internal DNS queries to an upstream resolver.

> A forwarder lets domain DNS keep control of internal records while sending unknown external names to a trusted resolver.

![DC1 External Forwarder](../../images/07-dns/08-dc1-external-forwarder.png)

<p><sub><strong>Screenshot 072 - DC1 External Forwarder:</strong> DC1 external DNS forwarder configured for recursive resolution.</sub></p>

---

### Step 03 - Create a controlled zone for Facebook blocking

The lab creates a DNS zone for `facebook.com` and adds a controlled host record that prevents normal resolution.

> Controlled DNS zones show how name resolution can block or redirect access, which helps explain defensive filtering and malicious DNS manipulation.

![Facebook Zone Name](../../images/07-dns/11-facebook-zone-name.png)

<p><sub><strong>Screenshot 075 - Facebook Zone Name:</strong> Facebook.com zone name configuration.</sub></p>

---

### Step 04 - Validate the Facebook resolution result

The client test shows that the configured DNS response prevents normal access to the target domain.

> Testing from the client confirms that the DNS control affects endpoint traffic, not only DNS Manager configuration.

![Facebook Resolution Block Validation](../../images/07-dns/13-facebook-resolution-block-validation.png)

<p><sub><strong>Screenshot 077 - Facebook Resolution Block Validation:</strong> Client validation showing blocked or unreachable Facebook resolution.</sub></p>

---

### Step 05 - Discover Google name servers

`nslookup` runs from the Windows 10 client with query type `NS`. The client queries DC1 at `192.168.116.200` and receives Google's authoritative name servers.

> The response confirms that the client uses DC1 for DNS and that DC1 can resolve external records. `Server: Unknown` only means no reverse PTR exists for the DNS server address.

![Google nslookup Output](../../images/07-dns/14-google-nslookup-output.png)

<p><sub><strong>Screenshot 078 - Google nslookup Output:</strong> `nslookup` output for Google DNS records.</sub></p>

---

### Step 06 - Configure a Google conditional forwarder

DC1 is configured with a conditional forwarder for `google.com` that sends matching queries to `ns1.google.com` at `216.239.32.10`. Queries for other domains continue to use the server's normal DNS resolution path.

> Conditional forwarding sends one namespace to designated DNS servers without changing resolution for other domains. Google is used here only to demonstrate the mechanism.

![Google Conditional Forwarder](../../images/07-dns/15-google-conditional-forwarder.png)

<p><sub><strong>Screenshot 079 - Google Conditional Forwarder:</strong> Google conditional forwarder configured with a master server.</sub></p>

---

### Step 07 - Configure a Yahoo stub zone

A stub zone for `yahoo.com` is created from `ns2.yahoo.com` at `68.142.255.16`. DC1 uses it to obtain SOA, NS, and glue records for Yahoo's authoritative DNS servers.

> Unlike a conditional forwarder, a stub zone keeps a small, refreshable list of authoritative servers without storing the full external zone.

![Yahoo Stub Zone Master Servers](../../images/07-dns/17-yahoo-stub-zone-master-servers.png)

<p><sub><strong>Screenshot 081 - Yahoo Stub Zone Master Servers:</strong> Stub zone master servers for Yahoo.</sub></p>

---

### Step 08 - Create a secondary DNS zone

The lab creates a primary zone on DC1 and a secondary zone on DC2. The secondary zone receives data from the primary DNS server.

> A secondary zone is a read-only copy transferred from a primary DNS server, improving availability for name resolution.

![Secondary Zone Master IP](../../images/07-dns/21-secondary-zone-master-ip.png)

<p><sub><strong>Screenshot 085 - Secondary Zone Master IP:</strong> Primary DNS server IP used for secondary zone transfer.</sub></p>

---

### Step 09 - Create and validate a New Host record

The **New Host** action creates an `A` record that maps a host name to an IPv4 address. Here, `mail` points to DC1 at `192.168.116.200`, creating `mail.samueldomain.com`.

> Host records let users and applications reach systems by name instead of IP address. The `ping -a mail` test confirms that the name resolves to the intended server.

![Mail A Record Created](../../images/07-dns/23-mail-a-record-created.png)

<p><sub><strong>Screenshot 087 - Mail A Record Created:</strong> Mail A record created for lab name-resolution validation.</sub></p>

PC1 resolves `mail.samueldomain.com` to `192.168.116.200` and receives replies from DC1, confirming that the record is available through internal DNS.

![Mail Host Record Validation](../../images/07-dns/24-mail-record-ping-validation.png)

<p><sub><strong>Screenshot 088 - Mail Host Record Validation:</strong> PC1 resolving and reaching the new `mail.samueldomain.com` host record.</sub></p>

---

### Step 10 - Configure and validate DNS round robin

DNS round robin places several IP addresses behind one host name. DC1 rotates the address order in responses so requests can be distributed across equivalent servers.

Three `A` records are created with the same test hostname and different Google server addresses. Round robin is enabled on DC1, then repeated `nslookup` queries from PC1 confirm that the returned order changes.

> Round robin can spread basic requests, but it is not full load balancing because DNS does not check server health and client caching can affect selection.

![Round Robin Host Records](../../images/07-dns/25-round-robin-host-records.png)

<p><sub><strong>Screenshot 089 - Round Robin Host Records:</strong> Three host records using the same name and different IP addresses for round robin.</sub></p>

![Enable DNS Round Robin](../../images/07-dns/26-round-robin-enabled.png)

<p><sub><strong>Screenshot 090 - Enable DNS Round Robin:</strong> Round robin enabled in the advanced properties of the DC1 DNS service.</sub></p>

![Round Robin Validation](../../images/07-dns/27-round-robin-validation.png)

<p><sub><strong>Screenshot 091 - Round Robin Validation:</strong> Repeated `nslookup` queries from PC1 showing the returned addresses in a different order.</sub></p>

---

## Validation and Summary


Validation confirms DNS client settings, forwarders, zones, `nslookup` output, ping tests, secondary-zone visibility, and client-side round robin behavior.


This chapter validates domain DNS settings, forwarding, zones, host records, and basic round robin behavior.

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
