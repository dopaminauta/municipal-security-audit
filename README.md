# 🔐 External Security Assessment — Municipal Web Infrastructure (Case Study)

[![Assessment Type](https://img.shields.io/badge/assessment-passive%20OSINT-blue)](https://github.com/axelyd/municipal-security-audit)
[![Methodology](https://img.shields.io/badge/disclosure-coordinated%20CVD-green)](https://github.com/axelyd/municipal-security-audit)
[![Exploitation](https://img.shields.io/badge/exploitation-none-brightgreen)](https://github.com/axelyd/municipal-security-audit)
[![Review](https://img.shields.io/badge/review-ethical%20QA%20gate-purple)](https://github.com/axelyd/municipal-security-audit)
[![Status](https://img.shields.io/badge/status-case%20study-orange)](https://github.com/axelyd/municipal-security-audit)

**Security assessment case study** — demonstrating real-world vulnerability research, OSINT methodology, and responsible disclosure practices.

> ⚠️ **Important:** This repository contains a **fictionalized case study** based on a real security assessment, prepared for professional portfolio purposes. All technical information, domain names, IP addresses, service identifiers, geographic data layers, internal hostnames, and attack vectors have been **replaced with synthetic or simulated data** to protect the confidentiality of the assessed organization, in accordance with Coordinated Vulnerability Disclosure (CVD) principles.

---

## 📋 Overview

This case study demonstrates a complete external security assessment workflow: from reconnaissance and service fingerprinting through vulnerability analysis, severity scoring, and prioritized remediation planning.

**All work was performed passively — no exploits were executed, no systems were accessed, no data was modified or extracted.**

A detailed confidential report was delivered to the system owner prior to the publication of this anonymized case study.

---

## 🎯 Key Findings

| Severity | Count | Category |
|----------|:-----:|----------|
| 🔴 Critical/High | 3 | Unauthenticated data exposure, CORS misconfiguration, cross-border data compliance |
| 🟡 Medium | 2 | Information leakage via errors, insecure cookie configuration |
| 🟢 Low/Info | 1 | Missing HTTP security headers (9 total) |

**Most Significant:** An unauthenticated GeoServer WFS endpoint allowed bulk download of a municipal geospatial database — with records spanning 35+ years — to any internet user without credentials.

---

## 🛠️ Skills Demonstrated

| Domain | Techniques |
|--------|------------|
| **OSINT** | Certificate transparency (crt.sh), Shodan InternetDB, DNS enumeration, WHOIS, Wayback Machine, gau |
| **Web Security** | CORS policy analysis, CVSS 3.1 scoring, HTTP security header auditing, session management review |
| **Vulnerability Research** | CVE-to-version matching, GeoServer attack surface analysis, CWE mapping |
| **Network Reconnaissance** | nmap service discovery, technology stack fingerprinting (whatweb), nuclei scanning |
| **Compliance & Risk** | Cross-border data protection assessment, sensitive data classification, regulatory impact analysis |
| **Technical Writing** | Executive summaries, CVSS-justified findings, prioritized remediation roadmaps, synthetic PoCs |
| **Quality Assurance** | Multi-agent AI review pipeline, CVSS calibration, finding validation |

---

## 📁 Repository Structure

```
municipal-security-audit/
├── README.md                      ← Portfolio overview & methodology
├── SECURITY_AUDIT_REPORT.md       ← Full case study report (fictionalized)
└── LICENSE                        ← MIT License
```

---

## 🧰 Methodology & Tools

| Phase | Tools Used |
|-------|------------|
| **OSINT** | dig, whois, crt.sh, Shodan, gau, Wayback Machine |
| **Scanning** | nmap, whatweb, nuclei |
| **Verification** | curl, manual HTTP analysis |
| **CVE Analysis** | NIST NVD, MITRE CVE database |
| **Quality Review** | AI-assisted ethical & technical validation |

**Standards:** OWASP Testing Guide v4 · NIST SP 800-115 · OSSTMM 3.0

---

## 🛡️ Ethics & Coordinated Disclosure

This case study follows **Coordinated Vulnerability Disclosure (CVD)** as described in *Gray Hat Hacking, 6th Edition*:

1. **100% Passive:** No active exploitation, no intrusion, no denial of service
2. **OSINT-Only:** All intelligence from public sources only
3. **Confidential First:** A complete confidential report was provided to the system owner before any public disclosure
4. **Structural Sanitization:** All identifying information has been replaced with synthetic data for this public version
5. **No Exploit Code:** This repository contains no working exploits, no real target data, and no bypass techniques against live systems

---

## 📊 Assessment Metrics (Fictionalized)

| Metric | Value |
|--------|:-----:|
| Subdomains Discovered | 15 |
| IP Addresses Identified | 5 |
| Technologies Fingerprinted | 7 (Apache, nginx, Jetty, GeoServer, PostgreSQL, ASP.NET, React/Vite) |
| CVEs Evaluated | 18 |
| Findings Documented | 6 |
| References & Standards | OWASP, NIST, OSSTMM |

---

## 📄 Full Case Study

→ **[SECURITY_AUDIT_REPORT.md](./SECURITY_AUDIT_REPORT.md)**

---

*Professional security portfolio. For inquiries, contact the repository maintainer. Unauthorized use of the techniques described against systems you do not own or have written permission to test is illegal.*
