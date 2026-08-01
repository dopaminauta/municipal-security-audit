# Security Assessment Methodology - Lab Case Study (Synthetic Municipal GeoServer)

![type](https://img.shields.io/badge/type-methodology%20case%20study-blue)
![scenario](https://img.shields.io/badge/scenario-synthetic%20%2F%20lab-purple)
![exploitation](https://img.shields.io/badge/exploitation-none-brightgreen)

**A fully synthetic, educational case study** that demonstrates an end-to-end external security assessment methodology - reconnaissance, service fingerprinting, CVE correlation, CVSS scoring, and prioritized remediation writing - on a **fabricated "municipal" web estate**.

> **No real organization was assessed, contacted, or scanned to produce this repo.** The target, subdomains, IPs (RFC 5737 documentation ranges), and every finding are invented to exercise the workflow. Because the scenario is a self-owned lab, active tooling (nmap, nuclei) is used and described honestly - see the methodology note about why that is only acceptable on lab/authorized targets.

## What it demonstrates
- Structuring an external assessment (OSINT -> fingerprinting -> CVE correlation -> scoring -> remediation).
- **Severity discipline:** treating version-string CVE matches as *hypotheses*, not confirmed criticals.
- Clear, CVSS-justified findings and a prioritized remediation roadmap.
- Knowing the boundary: active scanning belongs on lab or authorized targets only; real unauthorized targets get passive OSINT at most, or nothing.

## Contents
```
README.md                 - this overview
SECURITY_AUDIT_REPORT.md  - the full synthetic case-study report
LICENSE                   - MIT
```

## Findings modeled (synthetic)
| # | Finding | Severity |
|---|---------|:--------:|
| 1 | Unauthenticated GeoServer WFS exposure | Medium (5.3) |
| 2 | CORS misconfiguration (wildcard + credentials) | Medium (5.3) |
| 3 | Cross-border sensitive-data hosting (compliance) | Observation |
| 4 | Verbose error / internal info leak | Low (2.0) |
| 5 | Session cookies missing security attributes | Low (2.0) |
| 6 | Missing HTTP security headers | Info |

## Skills demonstrated
| Domain | Techniques |
|--------|------------|
| OSINT | Certificate transparency (crt.sh), Shodan InternetDB, DNS enumeration, WHOIS, gau, Wayback |
| Web security | CORS policy analysis, CVSS 3.1 scoring, HTTP security-header auditing, session review |
| Vulnerability research | CVE-to-version matching, GeoServer attack-surface analysis, CWE mapping |
| Network recon | nmap service discovery, tech fingerprinting (whatweb), nuclei (lab only) |
| Compliance & risk | Cross-border data-protection assessment, sensitive-data classification |
| Technical writing | Executive summaries, CVSS-justified findings, prioritized remediation roadmaps |

## Standards referenced
OWASP Testing Guide v4 - NIST SP 800-115 - OSSTMM 3.0

## Author
**Axel Feduzka** · GitHub [@dopaminauta](https://github.com/dopaminauta) · dominguezfya@gmail.com

---
*Synthetic educational content. No exploit code against live systems, no real target data.*
