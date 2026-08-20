# 🛡️ OWASP Security Audit Workflow

[![Workflow](https://img.shields.io/badge/GitHub%20Actions-enabled-blue)](https://github.com/Ahmadib12/owasp-security-audit/actions)
[![License](https://img.shields.io/badge/license-MIT-lightgrey)](LICENSE)
[![OWASP Top 10 2025](https://img.shields.io/badge/OWASP-Top%2010%202025-orange)](https://owasp.org/www-project-top-ten/)

One-line: GitHub Actions workflow and local tooling to run SAST and IaC scans (Bandit, Semgrep, Checkov) and map findings to OWASP Top 10:2025 risks.

Table of contents
- [Quickstart (local)](#quickstart-local)
- [Example GitHub Actions workflow](#example-github-actions-workflow)
- [What the tools cover (mapping to OWASP Top 10:2025)](#what-the-tools-cover-mapping-to-owasp-top-102025)
- [OWASP Top 10:2025 Categories & CWE Mappings](#owasp-top-102025-categories--cwe-mappings)
- [Uploading & triaging results](#uploading--triaging-results)
- [Configuration & reducing noise](#configuration--reducing-noise)
- [Baseline & workflow for noisy repos](#baseline--workflow-for-noisy-repos)
- [Interpreting findings](#interpreting-findings)
- [Performance, security & permissions notes](#performance-security--permissions-notes)
- [Mermaid flowchart (visual)](#mermaid-flowchart-visual)
- [References](#references)
- [Contributing & Security](#contributing--security)
- [License](#license)

---

## Quickstart (local)

Prerequisites
- Python 3.8+ (Bandit, Semgrep)
- pip
- checkov (Python package)

Install
```bash
python -m pip install --upgrade pip
pip install bandit semgrep checkov
Run scans locally (examples)

bash
# Bandit (JSON)
bandit -r . -f json -o bandit.json || true

# Semgrep (OWASP policy pack)
semgrep --config "p/owasp-top-ten" --json --output semgrep.json . || true

# Checkov (SARIF)
checkov -d . --output sarif --output-file-path checkov_results.sarif || true
Expect output files:

bandit.json
semgrep.json
checkov_results.sarif
Quick tip: run scans against changed paths in PRs to reduce noise and runtime.

Example GitHub Actions workflow
Save as .github/workflows/owasp-scan.yml.

Notes included in the workflow:

Single steps: block with all three security scanners (Semgrep, Bandit, Checkov).
Limit permissions to minimal required.
Bandit JSON output for structured findings.
Artifact upload for manual review and triage.
YAML
name: OWASP Top 10 Security Audit

on:
  push:
    branches: [ "main", "master", "develop" ]
  pull_request:
    branches: [ "main", "master", "develop" ]

permissions:
  contents: read          # least privilege, only read repo contents
  security-events: write  # needed if you upload SARIF results

jobs:
  owasp-sast-scan:
    name: OWASP Code & IaC Security Scan
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Source Code
        uses: actions/checkout@v4

      # ---------------------------------------------------------------
      # 1. Semgrep: OWASP Top 10 Multi-Language Ruleset
      # ---------------------------------------------------------------
      - name: Run Semgrep (OWASP Ruleset)
        uses: semgrep/semgrep-action@v1
        with:
          config: >-
            p/owasp-top-10

      # ---------------------------------------------------------------
      # 2. Bandit: Python Security Rules (Injection, Crypto, Secrets)
      # ---------------------------------------------------------------
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.x'

      - name: Install & Run Bandit
        run: |
          pip install bandit
          # Scans recursively and saves JSON results
          bandit -r . -f json -o bandit.json

      - name: Upload Bandit JSON results
        uses: actions/upload-artifact@v4
        with:
          name: bandit-results
          path: bandit.json

      # ---------------------------------------------------------------
      # 3. Checkov: Infrastructure as Code & Security Misconfigurations
      # ---------------------------------------------------------------
      - name: Run Checkov Scan
        uses: bridgecrewio/checkov-action@master
        with:
          directory: '.'
          framework: 'all'
          output_format: 'cli'
          soft_fail: false
What the tools cover (mapping to OWASP Top 10:2025)
Bandit (Python) — identifies insecure code patterns, weak cryptography, and Python-specific vulnerabilities.
Maps to: A04 Cryptographic Failures, A05 Injection, A07 Authentication Failures.
Semgrep — rule-based static analysis with OWASP Top 10:2025 policy packs.
Maps to: A01 Broken Access Control, A05 Injection, A06 Insecure Design, A08 Software or Data Integrity Failures.
Checkov — Infrastructure-as-Code (IaC) scanning for Terraform, Kubernetes, Dockerfiles, CloudFormation, and cloud policies.
Maps to: A02 Security Misconfiguration, A03 Software Supply Chain Failures, A07 Authentication Failures.
For detailed CWE mappings, see OWASP-CWE-Mapping.md.

OWASP Top 10:2025 Categories & CWE Mappings
Rank	Category	Primary CWEs	Common Findings
A01:2025	Broken Access Control	CWE-284, CWE-285, CWE-639	Authorization bypass, missing ACL checks, insecure direct object references
A02:2025	Security Misconfiguration	CWE-16, CWE-611, CWE-250	Default credentials, exposed debug endpoints, misconfigured services, XXE
A03:2025	Software Supply Chain Failures	CWE-1104, CWE-494, CWE-829	Unmaintained dependencies, dependency tampering, unsigned downloads
A04:2025	Cryptographic Failures	CWE-327, CWE-328, CWE-311	Weak algorithms/hashes, missing encryption at rest/in transit
A05:2025	Injection	CWE-89, CWE-94, CWE-77	SQL injection, code/eval injection, command injection
A06:2025	Insecure Design	CWE-209, CWE-693, CWE-840	Architectural flaws, business-logic weaknesses, missing threat model mitigations
A07:2025	Authentication Failures	CWE-287, CWE-613, CWE-798	Broken/insufficient authentication, weak sessions, hard-coded credentials
A08:2025	Software or Data Integrity Failures	CWE-494, CWE-1104, CWE-347	Unsigned updates, tampered data, unvalidated deserialization
A09:2025	Security Logging and Alerting Failures	CWE-778, CWE-223	Missing/insufficient logs, no alerting on suspicious activity
A10:2025	Mishandling of Exceptional Conditions	CWE-703, CWE-391, CWE-209	Unchecked errors, unsafe exception handling, information exposure via errors
Note: This mapping aligns with OWASP Top 10:2025. For detailed CWE definitions and tool-specific coverage, see OWASP-CWE-Mapping.md.

Uploading & triaging results
Where results appear

Artifacts: JSON/SARIF files are saved as workflow artifacts and can be downloaded for manual triage.
SARIF uploads: SARIF files are uploaded to GitHub's Code scanning / Security tab, surfacing findings in PRs and the Security UI.
Triage guidance

Prioritize by severity and OWASP category:
Critical → fix immediately or block merge.
High → plan a hotfix or include in the next sprint.
Medium/Low → schedule or suppress if false positive.
Map findings to OWASP Top 10:2025 categories using the table above and OWASP-CWE-Mapping.md.
For noisy repos: create a baseline by running scans locally, triaging findings, and adding ignores for accepted items (see "Configuration & reducing noise").
Recording context in PRs

Add a short note linking to scan artifacts and Security alerts in the PR description so reviewers can verify fixes.
Example: link to workflow run artifacts and include the top 3 findings summary, categorized by OWASP Top 10:2025.
Configuration & reducing noise
Semgrep

Example .semgrep.yml snippet to set severities and rules (customize to repo needs).
YAML
rules:
  - id: experimental-rule
    patterns: ...
    severity: INFO
    message: "..."
.semgrepignore example:
Code
vendor/
third_party/
migrations/
node_modules/
Bandit

Exclude paths:
bash
bandit -r . -x path/to/exclude -f json -o bandit.json
Or configure a bandit config file to ignore specific tests.
Checkov

Create .checkov.yml to skip checks or configure paths; or use --skip-check CLI options.
Baseline approach

Run scans locally against a full checkout.
Triage and suppress clear false positives in config files (committed to a branch).
Commit suppression/config and re-run CI.
Periodically revisit suppressions to avoid staleness.
Baseline & workflow for noisy repos
Run full scans in an isolated branch and save artifacts.
Triage externally (spreadsheet or issue tracker) and generate suppression lists.
Map findings to OWASP Top 10:2025 categories using CWE identifiers.
Add suppressions to repository config (.semgrep.yml, .checkov.yml, Bandit config).
Switch CI to run targeted scans on PRs (changed files) and full scans on schedule.
Interpreting findings
SARIF/JSON fields to check: file path, line numbers, rule id, severity, and code snippet.
Map findings to OWASP Top 10:2025 using CWE IDs — consult OWASP-CWE-Mapping.md.
False positives happen: check context, search for tests/third-party code, and consider suppressing only after careful review.
Consider linking each major fix to a security issue/PR to track remediation and rationale, including the OWASP category addressed.
Performance, security & permissions notes
Performance

Avoid running heavy scans on every small commit: run full scans nightly and targeted scans on PRs.
Use pip caching or a custom runner image with preinstalled scanners.
Security

Keep workflow permissions minimal.
Never print secrets or credentials in logs; use GitHub Secrets for any credentials.
Validate security findings against OWASP Top 10:2025 standards before merge.
Costs & runner time

Installing scanners each run increases runtime — consider caching or prebuilt images.
Mermaid flowchart (visual)
Mermaid
flowchart LR
  A[Checkout] --> B[Install Python]
  B --> C[Run Semgrep]
  B --> D[Run Bandit]
  B --> E[Run Checkov]
  C --> F[Artifact / Code Scanning]
  D --> F
  E --> F
  F --> G[Map to OWASP Top 10:2025]
  G --> H[Triage & Fix]
References
OWASP Top 10:2025
OWASP-CWE-Mapping.md — detailed CWE mappings for this project
Bandit docs
Semgrep docs
Checkov docs
GitHub SARIF upload
MITRE CWE Top 25
Contributing & Security
Contributions welcome — open issues or PRs for:

New rules or config improvements aligned with OWASP Top 10:2025
Better triage guidance or automation
Performance optimizations
CWE mapping enhancements
Security disclosure

If you find a security issue with this workflow or the repository, please follow our SECURITY.md or open a private disclosure issue.
License
This repository is provided under the MIT License. See LICENSE.
