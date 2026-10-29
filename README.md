# Security Assessment Methodology - Municipal Public Surface (Authorized, Anonymized)

![type](https://img.shields.io/badge/type-methodology%20case%20study-blue)
![scope](https://img.shields.io/badge/scope-authorized%20assessment-green)
![exploitation](https://img.shields.io/badge/exploitation-none-brightgreen)

**An external assessment of a municipal organization's public surface**, carried out with written authorization from its systems team.

The organization is not named. Infrastructure details are generalized or replaced with documentation ranges so the repository is safe to publish, while the methodology and the reasoning behind each severity decision stay intact.

## Scope and authorization

- Written authorization from the organization's systems area, on file.
- Passive OSINT and analysis of publicly reachable surface. No exploitation, no authenticated access, no data extraction.
- Findings were shared with the organization's team before anything was published.
- What this repository publishes: methodology, severity reasoning, and remediation structure. Not the target identity, not live host details.

## What it demonstrates

- Structuring an external assessment end to end: OSINT, fingerprinting, CVE correlation, CVSS scoring, prioritized remediation.
- **Severity discipline:** version-string CVE matches are treated as *hypotheses*, not confirmed criticals.
- Clear, CVSS-justified findings and a remediation roadmap ordered by risk.
- Knowing the boundary: passive OSINT on a real target requires authorization, and nothing here goes past what that authorization covered.

## Contents

```
README.md                 - this overview
SECURITY_AUDIT_REPORT.md  - the assessment report (target details generalized)
LICENSE                   - MIT
```

## Findings (details generalized)

| # | Finding | Severity |
|---|---------|:--------:|
| 1 | Unauthenticated GeoServer WFS exposure | Medium (5.3) |
| 2 | CORS misconfiguration (wildcard + credentials) | Medium (5.3) |
| 3 | Cross-border sensitive-data hosting (compliance) | Observation |
| 4 | Verbose error / internal info leak | Low (2.0) |
| 5 | Session cookies missing security attributes | Low (2.0) |
| 6 | Outdated CMS components pending version confirmation | Low (pending) |

## Notes

- Hostnames, IPs, and identifiers in the report are generalized or use RFC 5737 documentation ranges.
- The report is a methodology document as much as a record: it keeps the reasoning, not the target's fingerprints.
