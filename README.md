# 🔐 External Security Assessment — Municipal Web Infrastructure (Case Study)

[![Assessment Type](https://img.shields.io/badge/assessment-passive%20OSINT-blue)](https://github.com/dopaminauta/municipal-security-audit)
[![Methodology](https://img.shields.io/badge/disclosure-coordinated%20CVD-green)](https://github.com/dopaminauta/municipal-security-audit)
[![Exploitation](https://img.shields.io/badge/exploitation-none-brightgreen)](https://github.com/dopaminauta/municipal-security-audit)
[![Review](https://img.shields.io/badge/review-ethical%20QA%20gate-purple)](https://github.com/dopaminauta/municipal-security-audit)
[![Status](https://img.shields.io/badge/status-case%20study-orange)](https://github.com/dopaminauta/municipal-security-audit)

**Security assessment case study** — demonstrating real-world vulnerability research, OSINT methodology, and responsible disclosure practices.

>  **Important:** This repository contains a **fictionalized case study** based on an unsolicited good-faith external security assessment, prepared for professional portfolio purposes. All technical information, domain names, IP addresses, service identifiers, geographic data layers, internal hostnames, and attack vectors have been **replaced with synthetic or simulated data** to protect the confidentiality of the assessed organization, in accordance with Coordinated Vulnerability Disclosure (CVD) principles. The original confidential report was delivered to the system owner and a standard CVD remediation window elapsed before publication.

---

##  Overview

This case study demonstrates a complete external security assessment workflow: from reconnaissance and service fingerprinting through vulnerability analysis, severity scoring, and prioritized remediation planning.

**All work was performed passively — no exploits were executed, no systems were accessed, no data was modified or extracted.**

A detailed confidential report was delivered to the system owner prior to the publication of this anonymized case study.

---

##  Key Findings

| Severity | Count | Category |
|----------|:-----:|----------|
|  Unconfirmed CVE Exposure (version-matched) | 4 | CVE-2022-24816 (10.0), CVE-2024-36401 (9.8), CVE-2023-35042 (9.8), CVE-2023-25157 (9.8) — GeoServer RCE & SQLi vectors, not exploited |
|  Potentially Applicable CVE | 1 | CVE-2024-38473 (8.1) — Apache HTTPd, version range overlap, unconfirmed |
| 🟡 Verified Finding (CVSS 5.3) | 2 | Unauthenticated GeoServer WFS data exposure, CORS misconfiguration on production API |
| 🟡 Low (CVSS 2.0) | 2 | Information leakage via errors, insecure cookie configuration |
| ⚪ Compliance Observation | 1 | Cross-border health data hosting — regulatory risk |
|  Info | 1 | Missing HTTP security headers (9 total) |

**18 CVEs screened** via version fingerprinting across GeoServer + Jetty + Apache + Postfix. Multiple RCE vectors identified in the deployed software stack (not exploited — applicability confirmed via version match only).

**Most Significant:** A severely outdated GeoServer instance (unpatched since 2017) exposed an unauthenticated WFS endpoint allowing bulk download of the municipal geospatial database. The deployed software version carries several documented CVEs including **CVE-2022-24816 (CVSS 10.0)** and **CVE-2024-36401 (CVSS 9.8)** — unauthenticated RCE vectors confirmed applicable via version fingerprinting, though not exploited during this passive assessment.

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

##  Repository Structure

```
municipal-security-audit/
├── README.md                      <- Portfolio overview & methodology
├── SECURITY_AUDIT_REPORT.md       <- Full case study report (fictionalized)
└── LICENSE                        <- MIT License
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

##  Ethics & Coordinated Disclosure

This case study follows **Coordinated Vulnerability Disclosure (CVD)** as described in *Gray Hat Hacking, 6th Edition*:

1. **100% Passive:** No active exploitation, no intrusion, no denial of service
2. **OSINT-Only:** All intelligence from public sources only
3. **Confidential First:** A complete confidential report was provided to the system owner before any public disclosure
4. **Structural Sanitization:** All identifying information has been replaced with synthetic data for this public version
5. **No Exploit Code:** This repository contains no working exploits, no real target data, and no bypass techniques against live systems

---

##  Assessment Metrics (Fictionalized)

| Metric | Value |
|--------|:-----:|
| Subdomains Discovered | 15 |
| IP Addresses Identified | 7 |
| Technologies Fingerprinted | 7 (Apache, nginx, Jetty, GeoServer, Postfix, ASP.NET, React/Vite) |
| CVEs Evaluated | 18 |
| Findings Documented | 6 |
| References & Standards | OWASP, NIST, OSSTMM |

---

## 📄 Full Case Study

-> **[SECURITY_AUDIT_REPORT.md](./SECURITY_AUDIT_REPORT.md)**

---

*Professional security portfolio. For inquiries, contact the repository maintainer. Unauthorized use of the techniques described against systems you do not own or have written permission to test is illegal.*
