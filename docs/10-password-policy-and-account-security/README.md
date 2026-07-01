# Password Policy and Account Security

This chapter covers Password Policy and Account Security in the Windows Server infrastructure lab. It explains what was configured, why the configuration matters, and which evidence validates the result.


## Technical Context

The lab configures a domain-wide password baseline through Group Policy. A centrally managed policy ensures that domain users follow the same credential requirements regardless of which joined workstation they use.

Password policy is only one layer of account defense. It should support, not replace, account lockout policy, MFA, privileged account monitoring, and a clear process for handling compromised credentials.

**Implemented controls:**

- Configured domain password history and age requirements.
- Enforced minimum password length and complexity.
- Linked the `PassPolicy` GPO at the domain level so it can provide the password baseline for domain accounts.

## Key Technical Terms

| Term | Meaning in this chapter |
|------|-------------------------|
| Domain password policy | A centrally managed baseline for domain account password rules. |
| Password history | A setting that prevents users from immediately reusing recent passwords. |
| Minimum password age | A setting that prevents rapid password cycling to bypass history. |
| Complexity requirement | Windows rule requiring multiple character categories and rejecting simple account-name-based passwords. |
| RID 500 | The well-known identifier of the built-in Administrator account, which makes it predictable to attackers. |

---

## Detailed Walkthrough

### Step 01 - Configure the domain password policy

The `PassPolicy` GPO defines a consistent password baseline for domain accounts. It remembers the previous eight passwords, sets a maximum password age of 75 days, prevents another password change for 30 days, requires at least eight characters, and enables Windows complexity requirements.

Each setting addresses a different credential risk:

- **Password history: 8 passwords** prevents users from immediately reusing recent credentials.
- **Maximum password age: 75 days** limits how long the same password remains active in this lab. In modern production environments, forced periodic changes should be balanced against current security guidance and should always occur after suspected compromise.
- **Minimum password age: 30 days** prevents users from rapidly changing passwords several times to bypass password history. This value is intentionally strict for the exercise; production environments normally use a shorter minimum age to avoid blocking legitimate password changes.
- **Minimum length: 8 characters** increases the number of possible password combinations. For production systems, longer password phrases are preferable because length usually provides stronger resistance to guessing and offline cracking than complexity alone.
- **Complexity enabled** requires a mixture of character categories and prevents use of the account name inside the password. This helps reject simple, predictable choices, but it should be combined with longer passwords and known-compromised-password screening.
- **Reversible encryption: Not Defined** means this GPO does not enable that option. Reversible password storage should remain disabled unless a documented legacy requirement makes it unavoidable, because it provides protection similar to storing plaintext credentials.

> A domain password policy reduces the risk of weak passwords, repeated credential reuse, password-history bypass, and long-lived compromised credentials. Linking the policy at the domain root is important because domain-account password settings are processed as a domain-wide security baseline.

![Domain Password Policy Settings](../../images/11-password-policy/01-domain-password-policy-settings.png)

<p><sub><strong>Screenshot 144 - Domain Password Policy Settings:</strong> Domain password policy settings.</sub></p>

The `PassPolicy` GPO is linked directly beneath `SamuelDomain.com` in Group Policy Management. This placement makes the policy part of the domain-level configuration rather than a setting limited to one workstation or user OU.

![Domain Password Policy Linked](../../images/11-password-policy/02-password-policy-linked.png)

<p><sub><strong>Screenshot 145 - Domain Password Policy Linked:</strong> PassPolicy linked at the SamuelDomain.com domain level.</sub></p>

## Validation and Summary


Validation is based on the domain password-policy settings screen and the PassPolicy GPO link under the SamuelDomain.com domain.


This chapter documents the password baseline for the lab and explains the security value and production caveats of password history, age, length, complexity, and reversible-encryption settings.

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
