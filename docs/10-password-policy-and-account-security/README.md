# Password Policy and Account Security

This chapter documents the domain password policy baseline and why account-security settings matter in a Windows domain.


## Technical Context

A domain-wide password baseline is configured through Group Policy so users follow the same credential requirements on every joined workstation.

Password policy is only one layer of account defense and should be paired with lockout policy, MFA, privileged-account monitoring, and compromised-credential procedures.

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

The `PassPolicy` GPO remembers the previous eight passwords, sets a 75-day maximum age, prevents changes for 30 days, requires at least eight characters, and enables Windows complexity requirements.

Each setting addresses a different credential risk:

- **Password history: 8 passwords** prevents users from immediately reusing recent credentials.
- **Maximum password age: 75 days** limits how long the same password remains active in this lab. In modern production environments, forced periodic changes should be balanced against current security guidance and should always occur after suspected compromise.
- **Minimum password age: 30 days** prevents users from rapidly changing passwords several times to bypass password history. This value is intentionally strict for the exercise; production environments normally use a shorter minimum age to avoid blocking legitimate password changes.
- **Minimum length: 8 characters** increases the number of possible password combinations. For production systems, longer password phrases are preferable because length usually provides stronger resistance to guessing and offline cracking than complexity alone.
- **Complexity enabled** requires a mixture of character categories and prevents use of the account name inside the password. This helps reject simple, predictable choices, but it should be combined with longer passwords and known-compromised-password screening.
- **Reversible encryption: Not Defined** means this GPO does not enable that option. Reversible password storage should remain disabled unless a documented legacy requirement makes it unavoidable, because it provides protection similar to storing plaintext credentials.

> A domain password policy reduces weak passwords, credential reuse, history bypass, and long-lived compromised credentials. Linking it at the domain root makes it a domain-wide baseline.

![Domain Password Policy Settings](../../images/11-password-policy/01-domain-password-policy-settings.png)

<p><sub><strong>Screenshot 144 - Domain Password Policy Settings:</strong> Domain password policy settings.</sub></p>

The `PassPolicy` GPO is linked beneath `SamuelDomain.com`, making it a domain-level setting rather than a workstation or OU-only policy.

![Domain Password Policy Linked](../../images/11-password-policy/02-password-policy-linked.png)

<p><sub><strong>Screenshot 145 - Domain Password Policy Linked:</strong> PassPolicy linked at the SamuelDomain.com domain level.</sub></p>

## Validation and Summary


Validation confirms the password-policy settings and the `PassPolicy` GPO link under `SamuelDomain.com`.


This chapter documents the lab password baseline and the security value of history, age, length, complexity, and reversible-encryption settings.

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
