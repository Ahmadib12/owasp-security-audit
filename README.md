# 🛡️ OWASP Security Audit Workflow

[![Workflow](https://img.shields.io/badge/GitHub%20Actions-enabled-blue)](https://github.com/Ahmadib12/owasp-security-audit/actions)
[![License](https://img.shields.io/badge/license-MIT-lightgrey)](LICENSE)
[![OWASP Top 10](https://img.shields.io/badge/OWASP-Top%2010-orange)](https://owasp.org/www-project-top-ten/)

One-line: GitHub Actions workflow and local tooling to run Semgrep, Bandit and Checkov scans and map findings to OWASP Top 10 (aligned to OWASP Top 10:2025 via OWASP-CWE-Mapping.md).

Table of contents
- Quickstart (local)
- Example GitHub Actions workflow (what this repo runs)
- OWASP → CWE mapping (how to use OWASP-CWE-Mapping.md)
- Uploading & triaging results
- Configuration & reducing noise
- Troubleshooting & notes
- References
- License

---

## Quickstart (local)

Prerequisites
- Python 3.x
- pip
- semgrep, bandit, checkov (optional to run locally)

Install
```bash
python -m pip install --upgrade pip
pip install semgrep bandit checkov
```

Run the same scanners locally (examples)
```bash
# Semgrep (OWASP rules)
semgrep --config "p/owasp-top-10" --json --output semgrep.json .

# Bandit (as used in the workflow)
pip install bandit
bandit -r . -l -iii

# Checkov (CLI)
checkov -d . --output cli
```

Expect these outputs (if run with the shown flags):
- semgrep.json (Semgrep JSON)
- Bandit stdout (Bandit flags shown)
- Checkov CLI output

---

## Example GitHub Actions workflow (this repository)

Workflow file: .github/workflows/owasp-scan.yml

Key implemented steps (mirror the workflow)
- Triggers: push and pull_request on branches "main", "master", "develop".
- Permissions:
  - contents: read
  - security-events: write
- Semgrep
  - uses: semgrep/semgrep-action@v1
  - config: `p/owasp-top-10`
- Bandit
  - uses: actions/setup-python@v5
  - installs bandit, runs: `bandit -r . -l -iii`
- Checkov
  - uses: bridgecrewio/checkov-action@master
  - with: `directory: '.'`, `framework: 'all'`, `output_format: 'cli'`, `soft_fail: false`

Notes:
- The workflow installs Bandit during the job and runs Semgrep and Checkov via actions.
- If you want SARIF uploads, artifact uploads, or caching, add steps for conversion/upload and cache actions (not included in the current workflow).

---

## OWASP → CWE mapping (alignment)

This repository includes OWASP-CWE-Mapping.md (OWASP Top 10:2025 aligned). Use it to standardize triage, reporting, and automation.

How to use the mapping:
- Semgrep: add `cwe:` metadata to custom rules or map Semgrep rule IDs to CWE IDs in post-processing. Example Semgrep metadata:
  ```yaml
  metadata:
    cwe: "CWE-89"
  ```
- Bandit: map Bandit test IDs to CWE(s) in a small lookup table in your CI or triage scripts.
- Checkov: map Checkov rule IDs to CWE(s) using the YAML in OWASP-CWE-Mapping.md (a machine-readable example is included in that file).

Practical integration ideas:
- Post-process tool outputs and attach the matching CWE(s) from OWASP-CWE-Mapping.md to every finding before uploading to tracking systems or SARIF.
- Use the included owasp-cwe-mapping.yml example (from OWASP-CWE-Mapping.md) as a canonical lookup in CI conversion scripts.

---

## Uploading & triaging results

Where results appear
- The workflow runs scanners; capture outputs and upload artifacts or convert to SARIF for GitHub Code scanning (add SARIF upload step if you want findings in Security UI).

Triage guidance
- Prioritize by severity: Critical / High / Medium / Low.
- Use OWASP-CWE-Mapping.md to tag findings with CWE(s) and map them to OWASP categories for consistent tracking.
- For noisy repos: establish a baseline, triage findings, commit suppressions, and run targeted scans on PRs.

Recording context in PRs
- Link to workflow artifacts and list the top findings and mapped CWE/OWASP category to help reviewers.

---

## Configuration & reducing noise

Semgrep
- Use `.semgrep.yml` to enable/disable rules and set severities.
- Use `.semgrepignore` to exclude vendor/third_party/node_modules etc.

Bandit
- Use `-x` or a Bandit config to exclude paths or tests.

Checkov
- Use `.checkov.yml` or `--skip-check` to skip specific checks.

Baseline workflow
1. Run full scans locally.
2. Triage and add suppressions to config files.
3. Commit suppressions and re-run CI to establish a stable baseline.
4. Run targeted scans for PRs and full scans on a schedule.

---

## Troubleshooting & notes

- The repository workflow installs Bandit at runtime; consider caching pip or using a prebuilt runner image to save time.
- Confirm Bandit flags (`-l -iii`) are what you expect; consider producing JSON output (`-f json -o bandit.json`) for easier automation.
- Keep OWASP-CWE-Mapping.md up to date if you change OWASP version or add custom rules—this README assumes OWASP Top 10:2025 alignment.

---

## References

- OWASP Top 10: https://owasp.org/www-project-top-ten/
- OWASP-CWE-Mapping.md (this repo)
- Semgrep docs: https://semgrep.dev/docs/
- Bandit docs: https://bandit.readthedocs.io/
- Checkov docs: https://www.checkov.io/

---

## License

This repository is provided under the MIT License. See LICENSE.
