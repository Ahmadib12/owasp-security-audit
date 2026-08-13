# 🛡️ OWASP Top 10 – CWE Alignment

This file maps each **OWASP Top 10 risk category** to relevant **CWE identifiers**.  
It ensures that every vulnerability reported by Bandit, Semgrep, or Checkov can be traced to a standardized weakness definition.

---

## 🔑 OWASP Top 10 with CWE References

### A01 – Broken Access Control
- CWE-284: Improper Access Control  
- CWE-285: Improper Authorization  
- CWE-639: Authorization Bypass Through User-Controlled Keys  

### A02 – Cryptographic Failures
- CWE-327: Use of a Broken or Risky Cryptographic Algorithm  
- CWE-328: Use of Weak Hash  
- CWE-311: Missing Encryption of Sensitive Data  

### A03 – Injection
- CWE-89: SQL Injection  
- CWE-94: Code Injection  
- CWE-77: Command Injection  

### A04 – Insecure Design
- CWE-209: Information Exposure Through Error Messages  
- CWE-693: Protection Mechanism Failure  
- CWE-840: Business Logic Errors  

### A05 – Security Misconfiguration
- CWE-16: Configuration  
- CWE-611: Improper Restriction of XML External Entity Reference  
- CWE-478: Missing Default Case in Switch Statement  

### A06 – Vulnerable and Outdated Components
- CWE-1104: Use of Unmaintained Third Party Components  
- CWE-937: OWASP Top Ten 2017 Category A9  

### A07 – Identification & Authentication Failures
- CWE-287: Improper Authentication  
- CWE-613: Insufficient Session Expiration  
- CWE-798: Use of Hard-coded Credentials  

### A08 – Software Integrity Failures
- CWE-494: Download of Code Without Integrity Check  
- CWE-829: Inclusion of Functionality from Untrusted Control Sphere  

### A09 – Security Logging & Monitoring Failures
- CWE-778: Insufficient Logging  
- CWE-223: Omission of Security-relevant Information  

### A10 – Server-Side Request Forgery (SSRF)
- CWE-918: Server-Side Request Forgery  
- CWE-441: Unintended Proxy or Intermediary  

---

## 📌 Usage in This Project
- **Bandit findings** → Map to CWE IDs for Python security issues.  
- **Semgrep findings** → Map to CWE IDs for OWASP Top 10 rules.  
- **Checkov findings** → Map to CWE IDs for IaC misconfigurations.  

This alignment ensures that every vulnerability reported can be traced to a **global standard (CWE)**, making audits and compliance easier.
