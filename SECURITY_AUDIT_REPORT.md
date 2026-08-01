# Security Assessment Methodology - Lab Case Study
## Municipal GeoServer Scenario (Synthetic)

**Report ID:** LAB-CS-001
**Type:** Methodology demonstration on a synthetic/lab scenario
**Author:** Axel Feduzka (github.com/dopaminauta)
**Classification:** Public - Educational Case Study

> **What this is:** A constructed, fully synthetic lab scenario built to demonstrate an external-assessment methodology end to end: reconnaissance, service fingerprinting, CVE correlation, CVSS scoring, and prioritized remediation writing. **The target, its infrastructure, subdomains, IPs, and all findings below are fabricated for teaching purposes. No real organization was assessed, contacted, or scanned to produce this document.** Because the environment is a lab under the author's control, active tooling (nmap, nuclei) is used freely and described honestly.

---

## Executive Summary

This case study walks a "municipal government" web estate (synthetic) through a full external methodology. The fabricated scenario is seeded with realistic-but-invented findings so the analysis, scoring, and remediation writing can be demonstrated on something that resembles real-world conditions.

Seeded findings: **2 technical issues** (CVSS 5.3 each), **1 compliance observation**, and **3 hardening items**. The scenario's stack is intentionally built on outdated software so CVE-to-version correlation can be shown - with the deliberate teaching point that **a version-string match is a hypothesis, not a confirmed vulnerability** (see the CVE section).

> **Teaching note on severity discipline:** The scenario stack matches several high-CVSS CVEs by version string (e.g. GeoServer RCE families). This case study deliberately does NOT inflate those into "critical findings." A banner/version match only justifies "potentially applicable, unconfirmed" until authorized active verification is performed. Overstating version matches as confirmed criticals is one of the most common ways a report loses credibility - so this lab treats them conservatively on purpose.

---

## 1. Infrastructure Overview (synthetic)

| Item | Detail |
|------|--------|
| **Domain** | `target-municipal.example` |
| **Primary IP** | `198.51.100.10` (RFC 5737 documentation range) |
| **Additional IPs** | `198.51.100.11` - `198.51.100.15` |
| **Web Server** | Apache (ports 80, 443) |
| **Mail** | Postfix SMTP (port 25) |
| **SSL** | Wildcard `*.target-municipal.example` |

---

## 2. Subdomains (15, synthetic)

| # | Subdomain | Service | Technology |
|---|-----------|---------|------------|
| 1 | `www.target-municipal.example` | Main Portal | Apache |
| 2 | `maps.target-municipal.example` | GeoServer + Jetty | Java/GeoServer |
| 3 | `services.target-municipal.example` | Services Portal | React/Vite SPA |
| 4 | `api.target-municipal.example` | Production API | - |
| 5 | `health.target-municipal.example` | Records System | nginx + ASP.NET |
| 6 | `mail.target-municipal.example` | Webmail | Apache |
| 7 | `smtp.target-municipal.example` | SMTP | Postfix |
| 8-15 | Additional public subdomains | Various | Apache, nginx |

---

## 3. Findings (seeded for the exercise)

### FINDING 1 - Unauthenticated GeoServer WFS Endpoint

| Field | Detail |
|-------|--------|
| **Severity** | MEDIUM (CVSS 5.3) |
| **CWE** | CWE-200: Exposure of Sensitive Information |
| **Location** | `maps.target-municipal.example/geoserver/wfs` |
| **CVSS Vector** | `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N` |

**Description:** In the scenario, a GeoServer WFS layer answers unauthenticated `GetFeature` requests, returning a bulk geospatial dataset. Cadastral/geospatial data is often public record, so severity is scored MEDIUM - it would rise to 7.5+ only if the layer contained PII (owner names, tax IDs).

**Illustrative request (against the synthetic host):**
```bash
curl -s "https://maps.target-municipal.example/geoserver/wfs?\
service=WFS&version=2.0.0&request=GetFeature&\
typeName=municipal_records:sensitive_layer" | wc -c
```

**Remediation:**
1. Require authentication on WFS layers (Basic/OAuth2/API keys).
2. Restrict WFS by IP to authorized ranges.
3. Run a GeoServer RBAC audit; review which layers truly need public access.
4. Rate-limit WFS to deter bulk scraping.

---

### FINDING 2 - CORS Misconfiguration on the API

| Field | Detail |
|-------|--------|
| **Severity** | MEDIUM (CVSS 5.3) |
| **CWE** | CWE-942: Permissive Cross-domain Policy with Credentials |
| **Location** | `api.target-municipal.example` |

**Description:** The scenario API returns `Access-Control-Allow-Origin: *` together with `Access-Control-Allow-Credentials: true`. Modern browsers reject that literal combination, so real-world impact depends on whether the server *reflects* arbitrary `Origin` values (which would raise CVSS toward 8.1). The teaching point: don't score the worst case until you confirm reflection.

**Illustrative check:**
```bash
curl -sI -H "Origin: https://attacker.example" \
  "https://api.target-municipal.example/" | grep -i "access-control"
```

**Remediation:** Replace the wildcard with an explicit allowlist; never pair a wildcard origin with credentials; validate origins server-side instead of reflecting them.

---

### FINDING 3 - [Compliance] Sensitive System Hosted Outside National Jurisdiction

| Field | Detail |
|-------|--------|
| **Severity** | N/A - Regulatory observation (no CVSS) |
| **Location** | `health.target-municipal.example` |

**Note:** A compliance observation, not a technical vulnerability - and a lesson in its own right: network fingerprinting cannot establish a legal data-controller's jurisdiction, so this is flagged for legal review, not scored.

**Remediation:** Legal review of cross-border health-data hosting; assess data-residency obligations; audit the provider's compliance posture.

---

### FINDING 4 - Internal Info Leak via Error Messages

| Field | Detail |
|-------|--------|
| **Severity** | LOW (CVSS 2.0) |
| **CWE** | CWE-209 |
| **Location** | `maps.target-municipal.example/geoserver/` |

Verbose GeoServer/Jetty errors reveal internal hostnames, backend versions, and stack traces. **Remediation:** suppress stack traces in production, use custom sanitized error pages, generic internal hostnames.

---

### FINDING 5 - Session Cookies Missing Security Attributes

| Field | Detail |
|-------|--------|
| **Severity** | LOW (CVSS 2.0) |
| **CWE** | CWE-614 |

`JSESSIONID` lacks explicit `Secure`/`SameSite`/`HttpOnly`. **Remediation (Jetty):**
```ini
jetty.httpConfig.secureCookies=true
jetty.httpConfig.sameSite=Lax
```

---

### FINDING 6 - Missing HTTP Security Headers

| Field | Detail |
|-------|--------|
| **Severity** | INFO |
| **CWE** | CWE-693 |

Nine headers absent (HSTS, CSP, X-Frame-Options, X-Content-Type-Options, Referrer-Policy, Permissions-Policy, CORP, COOP, COEP). **Remediation (Apache):**
```apache
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
Header always set X-Frame-Options "DENY"
Header always set X-Content-Type-Options "nosniff"
Header always set Referrer-Policy "strict-origin-when-cross-origin"
```

---

## 4. Methodology (as exercised in the lab)

| Phase | Activity | Tools |
|-------|----------|-------|
| F0 - OSINT | Domain/cert intelligence | dig, whois, crt.sh |
| F1 - Scanning | Service discovery, fingerprinting | nmap, whatweb, nuclei |
| F2 - Discovery | URL/history enumeration | gau, Wayback |
| F3 - Verification | Manual header/response analysis | curl |

**Reference frameworks:** OWASP Testing Guide v4, NIST SP 800-115, OSSTMM 3.0.

> Active scanning (nmap/nuclei) is used here because this is a self-owned lab scenario. **On any real target, active scanning requires prior written authorization or an in-scope VDP/bug-bounty program** - passive OSINT is the only defensible approach without it. This is the discipline the case study is built around.

---

## 5. CVE Analysis (version-match hypotheses, NOT confirmed)

| CVE | Component | CVSS | Status |
|-----|-----------|------|--------|
| CVE-2024-36401 | GeoServer RCE | 9.8 | Version match - unconfirmed, not exploited |
| CVE-2023-35042 | GeoServer RCE | 9.8 | Version match - unconfirmed, not exploited |
| CVE-2023-25157 | GeoServer SQLi | 9.8 | Version match - unconfirmed, not exploited |
| CVE-2022-24816 | GeoServer JAI-EXT | 10.0 | Version match - unconfirmed, not exploited |
| CVE-2024-38473 | Apache HTTPd | 8.1 | Version-range overlap only |

> Every row above is a **hypothesis from a version string**, not a demonstrated vulnerability. Confirming any of them would require authorized active testing, which is out of scope for a methodology demo. This distinction is the single most important discipline the case study means to teach.

---

## 6. Prioritized Remediation Roadmap

**Immediate:** authenticate WFS; fix CORS; suppress verbose errors.
**Short-term:** set security headers; harden cookies; legal review of data hosting.
**Medium-term:** upgrade GeoServer; add WAF rules; schedule recurring scanning.
**Ongoing:** patch-management policy; annual authorized pentests; stand up a VDP.

---

## 7. Limitations

- Synthetic scenario - illustrative, not a real assessment.
- Version-based CVE correlation yields hypotheses, not confirmations.
- External perspective only; no internal testing modeled.

---

## 8. References

- OWASP Testing Guide v4 - https://owasp.org/www-project-web-security-testing-guide/
- NIST SP 800-115
- GeoServer Security - https://docs.geoserver.org/stable/en/user/security/
- RFC 6454 (Web Origin / CORS)

---

*Synthetic educational case study. No real organization was assessed, contacted, or scanned. All hosts, IPs, and findings are fabricated to demonstrate methodology.*
