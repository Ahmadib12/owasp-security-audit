# 🛡️ OWASP Top 10 — CWE Alignment

**Owning repo:** Ahmadib12/owasp-security-audit  
**OWASP Top 10 version:** (specify e.g. 2021) — add the exact version/date used for this mapping.

This document maps each OWASP Top 10 risk category to representative CWE identifiers so findings from Bandit, Semgrep, Checkov (and other scanners) can be traced to standardized weakness definitions.

---

## Format / guidance
- Primary CWE(s): the most relevant CWE(s) for the OWASP category.  
- Secondary CWE(s): related or overlapping CWEs that may also apply.  
- Each CWE link goes to the canonical MITRE definition.

---

## OWASP Top 10 → CWE mapping

| OWASP | Title | Primary CWE(s) (links) | Notes / Example findings |
|---|---:|---|---|
| A01 | Broken Access Control | [CWE-284](https://cwe.mitre.org/data/definitions/284.html), [CWE-285](https://cwe.mitre.org/data/definitions/285.html) | Authorization bypass, missing ACL checks |
| A02 | Cryptographic Failures | [CWE-327](https://cwe.mitre.org/data/definitions/327.html), [CWE-328](https://cwe.mitre.org/data/definitions/328.html), [CWE-311](https://cwe.mitre.org/data/definitions/311.html) | Weak algorithms, missing encryption at rest/in transit |
| A03 | Injection | [CWE-89](https://cwe.mitre.org/data/definitions/89.html), [CWE-94](https://cwe.mitre.org/data/definitions/94.html), [CWE-77](https://cwe.mitre.org/data/definitions/77.html) | SQLi, command injection, eval/code injection |
| A04 | Insecure Design | [CWE-209](https://cwe.mitre.org/data/definitions/209.html), [CWE-693](https://cwe.mitre.org/data/definitions/693.html) | Design/architecture issues; business logic flaws |
| A05 | Security Misconfiguration | [CWE-16](https://cwe.mitre.org/data/definitions/16.html), [CWE-611](https://cwe.mitre.org/data/definitions/611.html) | Default creds, misconfigured servers, XXE |
| A06 | Vulnerable & Outdated Components | [CWE-1104](https://cwe.mitre.org/data/definitions/1104.html) | Use of unmaintained libraries, dependency issues |
| A07 | Identification & Authentication Failures | [CWE-287](https://cwe.mitre.org/data/definitions/287.html), [CWE-613](https://cwe.mitre.org/data/definitions/613.html), [CWE-798](https://cwe.mitre.org/data/definitions/798.html) | Broken auth, session issues, hard-coded creds |
| A08 | Software Integrity Failures | [CWE-494](https://cwe.mitre.org/data/definitions/494.html), [CWE-829](https://cwe.mitre.org/data/definitions/829.html) | Unsigned updates, insecure plugin inclusion |
| A09 | Security Logging & Monitoring Failures | [CWE-778](https://cwe.mitre.org/data/definitions/778.html), [CWE-223](https://cwe.mitre.org/data/definitions/223.html) | Missing centralized logs, insufficient audit trails |
| A10 | Server-Side Request Forgery (SSRF) | [CWE-918](https://cwe.mitre.org/data/definitions/918.html), [CWE-441](https://cwe.mitre.org/data/definitions/441.html) | Unrestricted URL fetches, open proxies |

> Note: The table above lists representative CWEs — for each concrete finding you may need to select the most specific CWE(s). Validate mappings against the MITRE CWE description.

---

## How to use this mapping in this project

- Bandit findings → add CWE(s) to the Bandit report output (example: in the security report, append CWE ID).
- Semgrep rules → include a `cwe` metadata field in each rule, e.g. `cwe: "CWE-89"`.
- Checkov/IaC findings → map Checkov rule IDs to the CWE(s) in a lookup table.

Example human-friendly mapping entry:
- Semgrep rule id: `python.django.security.insecure-redirect`
- Mapped CWEs: `CWE-601` (Open Redirect) — add to the findings export.

---

## Machine-readable mapping (example)
Save a mapping file (e.g., owasp-cwe-mapping.yml) and use it for automation.

```yaml
owasp_top10_version: "2021"            # update to the version you use
mappings:
  A01:
    title: "Broken Access Control"
    primary_cwes: [284, 285]
    notes: "Authorization bypass; missing ACL checks"
  A03:
    title: "Injection"
    primary_cwes: [89, 94, 77]
    notes: "SQL, code, command injection"
