# 🛡️ OWASP Top 10 — CWE Alignment


**OWASP Top 10 version:** 2025 — mapping aligned to OWASP Top 10:2025

This document maps each OWASP Top 10:2025 risk category to representative CWE identifiers so findings from Bandit, Semgrep, Checkov (and other scanners) can be traced to standardized weakness definitions.

---

## Format / guidance
- Primary CWE(s): the most relevant CWE(s) for the OWASP category.  
- Secondary CWE(s): related or overlapping CWEs that may also apply.  
- Each CWE link points to the canonical MITRE definition. Validate mappings against MITRE for specific findings.

---

## OWASP Top 10:2025 → CWE mapping

| OWASP | Title | Primary CWE(s) (links) | Notes / Example findings |
|---|---:|---|---|
| A01:2025 | Broken Access Control | [CWE-284](https://cwe.mitre.org/data/definitions/284.html), [CWE-285](https://cwe.mitre.org/data/definitions/285.html), [CWE-639](https://cwe.mitre.org/data/definitions/639.html) | Authorization bypass, missing ACL checks, insecure direct object refs |
| A02:2025 | Security Misconfiguration | [CWE-16](https://cwe.mitre.org/data/definitions/16.html), [CWE-611](https://cwe.mitre.org/data/definitions/611.html), [CWE-250](https://cwe.mitre.org/data/definitions/250.html) | Default creds, exposed debug endpoints, misconfigured services, XXE |
| A03:2025 | Software Supply Chain Failures | [CWE-1104](https://cwe.mitre.org/data/definitions/1104.html), [CWE-494](https://cwe.mitre.org/data/definitions/494.html), [CWE-829](https://cwe.mitre.org/data/definitions/829.html) | Unmaintained deps, dependency tampering, downloads without integrity checks |
| A04:2025 | Cryptographic Failures | [CWE-327](https://cwe.mitre.org/data/definitions/327.html), [CWE-328](https://cwe.mitre.org/data/definitions/328.html), [CWE-311](https://cwe.mitre.org/data/definitions/311.html) | Weak algorithms/hashes, missing encryption at rest/in transit |
| A05:2025 | Injection | [CWE-89](https://cwe.mitre.org/data/definitions/89.html), [CWE-94](https://cwe.mitre.org/data/definitions/94.html), [CWE-77](https://cwe.mitre.org/data/definitions/77.html) | SQLi, code/eval injection, command injection |
| A06:2025 | Insecure Design | [CWE-209](https://cwe.mitre.org/data/definitions/209.html), [CWE-693](https://cwe.mitre.org/data/definitions/693.html), [CWE-840](https://cwe.mitre.org/data/definitions/840.html) | Architectural/business-logic flaws, missing threat model mitigations |
| A07:2025 | Authentication Failures | [CWE-287](https://cwe.mitre.org/data/definitions/287.html), [CWE-613](https://cwe.mitre.org/data/definitions/613.html), [CWE-798](https://cwe.mitre.org/data/definitions/798.html) | Broken/insufficient auth, weak sessions, hard-coded creds |
| A08:2025 | Software or Data Integrity Failures | [CWE-494](https://cwe.mitre.org/data/definitions/494.html), [CWE-1104](https://cwe.mitre.org/data/definitions/1104.html), [CWE-347](https://cwe.mitre.org/data/definitions/347.html) | Unsigned updates, tampered data, unvalidated deserialization |
| A09:2025 | Security Logging and Alerting Failures | [CWE-778](https://cwe.mitre.org/data/definitions/778.html), [CWE-223](https://cwe.mitre.org/data/definitions/223.html) | Missing/insufficient logs, no alerting on suspicious activity |
| A10:2025 | Mishandling of Exceptional Conditions | [CWE-703](https://cwe.mitre.org/data/definitions/703.html), [CWE-391](https://cwe.mitre.org/data/definitions/391.html), [CWE-209](https://cwe.mitre.org/data/definitions/209.html) | Unchecked errors, unsafe exception handling, information exposure via errors |

> Note: These are representative mappings. For each finding choose the most specific CWE(s) and validate with MITRE.

---

## How to use this mapping in this project
- Bandit findings → append CWE(s) to Bandit report outputs or post-processing scripts.  
- Semgrep rules → add a `cwe:` metadata field in rules (e.g., `cwe: "CWE-89"`).  
- Checkov/IaC findings → maintain a lookup mapping from Checkov rule IDs to CWE IDs.

Example:
- Semgrep rule id: `python.django.security.insecure-redirect`  
- Mapped CWE(s): `CWE-601` (Open Redirect)

---

## Machine-readable mapping (example)
Save as owasp-cwe-mapping.yml for automation.

```yaml
owasp_top10_version: "2025"
mappings:
  A01:
    title: "Broken Access Control"
    primary_cwes: [284, 285]
    notes: "Authorization bypass; missing ACL checks"
  A02:
    title: "Security Misconfiguration"
    primary_cwes: [16, 611, 250]
    notes: "Default creds; exposed debug endpoints; misconfigured services"
  A03:
    title: "Software Supply Chain Failures"
    primary_cwes: [1104, 494, 829]
    notes: "Dependency tampering; downloads w/o integrity; unmaintained components"
  A04:
    title: "Cryptographic Failures"
    primary_cwes: [327, 328, 311]
  A05:
    title: "Injection"
    primary_cwes: [89, 94, 77]
  A06:
    title: "Insecure Design"
    primary_cwes: [209, 693, 840]
  A07:
    title: "Authentication Failures"
    primary_cwes: [287, 613, 798]
  A08:
    title: "Software or Data Integrity Failures"
    primary_cwes: [494, 1104, 347]
  A09:
    title: "Security Logging and Alerting Failures"
    primary_cwes: [778, 223]
  A10:
    title: "Mishandling of Exceptional Conditions"
    primary_cwes: [703, 391, 209]
