# External Security Assessment Report
## Municipal Web Infrastructure — Case Study

**Date:** July 2026  
**Report ID:** EXT-AUDIT-CS-001  
**Assessment Type:** External Passive Security Audit (OSINT + Non-Intrusive Scanning)  
**Auditor:** Independent Security Researcher  
**Reviewed by:** AI-assisted Quality Assurance  
**Classification:** Public Portfolio — Case Study (fictionalized for confidentiality)

> ⚠️ **Disclaimer:** This report is a fictionalized case study based on a real security assessment. All domain names, IP addresses, service names, internal hostnames, geographic data layers, and technical identifiers have been replaced with synthetic placeholders to protect the confidentiality of the assessed organization. This document follows Coordinated Vulnerability Disclosure (CVD) principles as described in *Gray Hat Hacking, 6th Edition*. No real infrastructure details, exploit paths, or sensitive data are disclosed.

---

## Executive Summary

An external security assessment was conducted on the web infrastructure of a municipal government entity using exclusively passive OSINT techniques and non-intrusive scanning. **No exploits were executed. No systems were accessed. No data was modified. No sensitive information was extracted.**

The assessment identified **15 subdomains**, **5 IP addresses**, and **6 security findings**, of which **3 are of critical/high severity**.

The most significant finding involves an **unauthenticated GeoServer WFS endpoint** exposing a geospatial database layer containing municipal records spanning over three decades — freely accessible to anyone on the internet without authentication.

---

## 1. Infrastructure Overview

| Item | Detail |
|------|--------|
| **Domain** | `target-municipal.example` |
| **Primary IP** | `198.51.100.10` |
| **Additional IPs** | `198.51.100.11` — `198.51.100.15` |
| **Web Server** | Apache (ports 80, 443) |
| **Mail** | Postfix SMTP (port 25) |
| **SSL** | Wildcard `*.target-municipal.example` (Let's Encrypt) |

---

## 2. Subdomains Discovered (15 total)

| # | Subdomain | Service | Technology |
|---|-----------|---------|------------|
| 1 | `www.target-municipal.example` | Main Portal | Apache |
| 2 | `maps.target-municipal.example` | GeoServer + Jetty | Java/GeoServer |
| 3 | `services.target-municipal.example` | Public Services Portal | React/Vite SPA |
| 4 | `api.target-municipal.example` | Production API | — |
| 5 | `health.target-municipal.example` | Health Records System | nginx + ASP.NET |
| 6 | `mail.target-municipal.example` | Webmail | Apache |
| 7 | `smtp.target-municipal.example` | SMTP | Postfix |
| 8-9 | `ns1/ns2.target-municipal.example` | DNS | `203.0.113.10` |
| 10-15 | Additional cultural/campaign subdomains | Various | Apache, nginx |

---

## 3. Security Findings

### 🔴 FINDING 1 — CRITICAL: Unauthenticated GeoServer WFS Endpoint — Geospatial Database Exposure

| Field | Detail |
|-------|--------|
| **Severity** | MEDIUM (CVSS 5.3) |
| **CWE** | CWE-200: Exposure of Sensitive Information |
| **Location** | `maps.target-municipal.example/geoserver/wfs` |
| **CVSS Vector** | `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N` |

> **QA Note:** CVSS calibrated to 5.3 — geospatial/cadastral data is typically public record. Severity would escalate to 7.5+ if the dataset includes personally identifiable information (property owner names, tax IDs, etc.).

**Description:**

The GeoServer WFS (Web Feature Service) endpoint exposes a municipal records layer without requiring authentication. A single unauthenticated HTTP GET request returns a multi-megabyte dataset containing:

- Record identifiers and registration dates
- Precise GPS geo-spatial polygons for each record
- Records spanning from the late 1980s to present day

**Impact:** Any internet user can download the complete municipal geospatial database without authentication, including property-level geographic data. While cadastral information may be public record, bulk unauthenticated download bypasses intended access controls and enables mass data harvesting.

**Verification (Synthetic PoC):**
```bash
# Generic WFS GetFeature request — demonstrates the vulnerability class
# Actual target details replaced with synthetic identifiers
curl -s "https://maps.target-municipal.example/geoserver/wfs?\
service=WFS&\
version=2.0.0&\
request=GetFeature&\
typeName=municipal_records:sensitive_layer" | wc -c

# Expected result: multi-megabyte response without authentication challenge
```

**Remediation:**
1. Implement authentication on all WFS layers in GeoServer (Basic Auth, OAuth2, or API keys)
2. Restrict WFS access by IP range to authorized municipal IPs only
3. Conduct a full GeoServer role-based access control (RBAC) audit
4. Consider rate-limiting WFS endpoints to prevent bulk data scraping
5. Review which layers truly need public WFS access vs. which should be internal-only

---

### 🟠 FINDING 2 — HIGH: CORS Misconfiguration on Production API

| Field | Detail |
|-------|--------|
| **Severity** | MEDIUM (CVSS 5.3) |
| **CWE** | CWE-942: Permissive Cross-domain Policy with Credentials |
| **Location** | `api.target-municipal.example` |

> **QA Note:** Modern browsers reject `Access-Control-Allow-Origin: *` combined with `Access-Control-Allow-Credentials: true` per the Fetch specification. However, if the server dynamically reflects arbitrary `Origin` header values rather than using a literal wildcard, CVSS escalates up to 8.1. Verification recommended.

**Description:**

The production API returns the following CORS response headers:
```http
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```

This combination is **explicitly prohibited** by RFC 6454 Section 7.1.2. When both headers are present, the browser is instructed to send authenticated requests (cookies, TLS client certificates) while allowing any origin to initiate them — a direct violation of the same-origin policy's security model.

**Verification (Synthetic PoC):**
```bash
# Test CORS header reflection
curl -sI -H "Origin: https://attacker.example" \
  "https://api.target-municipal.example/" \
  | grep -i "access-control"
```

**Remediation:**
1. Replace wildcard with an explicit allowlist of trusted origins
2. If credentials are required (`Access-Control-Allow-Credentials: true`), the `Allow-Origin` header **must never** be a wildcard
3. Implement dynamic origin validation server-side rather than reflecting the request's `Origin` header
4. Consider whether the API needs CORS at all (server-to-server APIs should not enable CORS)

---

### 🟠 FINDING 3 — HIGH: Sensitive Data System Hosted Outside National Jurisdiction

| Field | Detail |
|-------|--------|
| **Severity** | N/A (Compliance / Regulatory Risk) |
| **Location** | `health.target-municipal.example` |
| **Technology** | nginx + ASP.NET |

> **QA Note:** This is a compliance observation, not a technical vulnerability. Network-level fingerprinting (whatweb, dig) cannot establish the legal data controller's jurisdiction. Marked as a regulatory risk requiring legal assessment.

**Description:**

A health records management system handling sensitive citizen data is hosted on infrastructure located in a foreign jurisdiction (US-based datacenter). Under many national data protection frameworks, health data is classified as "sensitive personal data" subject to stricter handling requirements, including data residency or sovereignty considerations.

**Remediation:**
1. Conduct a legal review of applicable data protection regulations regarding cross-border health data hosting
2. Assess the data controller's obligations under relevant privacy legislation
3. Consider migration to infrastructure under national jurisdiction if required
4. Audit third-party hosting provider's security certifications and compliance framework

---

### 🟡 FINDING 4 — MEDIUM: Internal Infrastructure Information Leak via Error Messages

| Field | Detail |
|-------|--------|
| **Severity** | LOW (CVSS 2.0) |
| **CWE** | CWE-209: Generation of Error Message Containing Sensitive Information |
| **Location** | `maps.target-municipal.example/geoserver/` |

**Description:**

GeoServer WFS error responses expose internal infrastructure details including:
- Internal hostname and non-standard port of backend services
- Database engine type and version
- Data virtualization/abstraction layer details
- Full Java exception stack traces

**Impact:** Error message leakage provides attackers with a blueprint of the internal architecture, simplifying targeted attacks against specific backend components.

**Remediation:**
1. Configure GeoServer to suppress stack traces in production (`GEOSERVER_LOG_LEVEL=ERROR`, disable verbose error reporting)
2. Use generic, non-identifying hostnames in internal service configurations
3. Implement custom error pages that return sanitized error messages to external clients

---

### 🟡 FINDING 5 — MEDIUM: Session Cookies Missing Security Attributes

| Field | Detail |
|-------|--------|
| **Severity** | LOW (CVSS 2.0) |
| **CWE** | CWE-614: Sensitive Cookie Without Secure Attribute |
| **Location** | `maps.target-municipal.example/geoserver/` |

**Description:**

The JSESSIONID cookie set by the GeoServer application server is missing critical security flags:
- `SameSite` attribute not set (defaults to `Lax` in modern browsers, but explicit is better)
- `HttpOnly` flag not confirmed
- `Secure` flag should be set if served over HTTPS

**Remediation:**
```ini
# Jetty configuration example
jetty.httpConfig.secureCookies=true
jetty.httpConfig.sameSite=Lax
```

---

### 🟢 FINDING 6 — LOW: Missing HTTP Security Headers

| Field | Detail |
|-------|--------|
| **Severity** | INFO |
| **CWE** | CWE-693: Protection Mechanism Failure |
| **Location** | `maps.target-municipal.example/geoserver/` |

**Missing headers (9 total):**

| Header | Purpose |
|--------|---------|
| `Strict-Transport-Security` | Enforce HTTPS |
| `Content-Security-Policy` | Prevent XSS / data injection |
| `X-Frame-Options` | Prevent clickjacking |
| `X-Content-Type-Options` | Prevent MIME type sniffing |
| `Referrer-Policy` | Control referrer information leakage |
| `Permissions-Policy` | Restrict browser feature access |
| `Cross-Origin-Resource-Policy` | Control cross-origin resource loading |
| `Cross-Origin-Opener-Policy` | Isolate browsing contexts |
| `Cross-Origin-Embedder-Policy` | Control cross-origin embedding |

**Remediation:**
```apache
# Apache mod_headers configuration
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
Header always set X-Frame-Options "DENY"
Header always set X-Content-Type-Options "nosniff"
Header always set Referrer-Policy "strict-origin-when-cross-origin"
Header always set Permissions-Policy "geolocation=(), microphone=(), camera=()"
```

---

## 4. Tools & Methodology

| Phase | Activity | Tools & Techniques |
|-------|----------|-------------------|
| **F0 — OSINT** | Domain intelligence, certificate transparency | dig, whois, Shodan InternetDB, crt.sh |
| **F1 — Scanning** | Service discovery, technology fingerprinting | nmap, whatweb, nuclei |
| **F2 — Deep OSINT** | URL discovery, historical analysis | gau, Certspotter API, Wayback Machine |
| **F3 — Verification** | Manual validation, header analysis | curl, HTTP response inspection |

**Methodology Compliance:** OWASP Testing Guide v4, NIST SP 800-115, OSSTMM 3.0

---

## 5. CVE Analysis

| CVE | Component | CVSS | Status |
|-----|-----------|------|--------|
| CVE-2024-36401 | GeoServer RCE | 9.8 | Version match — not exploited |
| CVE-2023-35042 | GeoServer RCE | 9.8 | Version match — not exploited |
| CVE-2023-25157 | GeoServer SQLi | 9.8 | Version match — not exploited |
| CVE-2022-24816 | GeoServer JAI-EXT | 10.0 | Version match — not exploited |
| CVE-2024-38473 | Apache HTTPd | 8.6 | Potentially applicable |

> **Note:** CVE applicability was assessed via version fingerprinting only. **No exploits were attempted or executed.** A total of 18 CVEs were evaluated against the technology stack.

---

## 6. Prioritized Remediation Roadmap

### Immediate (0–7 days)
1. ✅ Implement authentication on all GeoServer WFS endpoints
2. ✅ Fix CORS configuration on the production API
3. ✅ Suppress verbose error messages in GeoServer

### Short-term (1–4 weeks)
4. Configure HTTP security headers on all web-facing services
5. Harden session cookie attributes
6. Conduct legal review of cross-border data hosting

### Medium-term (1–3 months)
7. Upgrade GeoServer to latest stable release with all security patches
8. Implement Web Application Firewall (WAF) rules for GeoServer endpoints
9. Establish quarterly vulnerability scanning schedule

### Ongoing
10. Adopt a Security Patch Management Policy
11. Conduct annual third-party penetration tests
12. Implement a Vulnerability Disclosure Program (VDP) to receive external security reports

---

## 7. Limitations

- **External Assessment Only:** No internal network testing was performed
- **Passive Methodology:** All findings based on publicly available information and non-intrusive fingerprinting
- **Snapshot in Time:** Findings represent infrastructure state as of the assessment date
- **No Exploitation:** Vulnerability applicability assessed via version matching; no exploits were executed

---

## 8. References

- OWASP Testing Guide v4: https://owasp.org/www-project-web-security-testing-guide/
- NIST SP 800-115 — Technical Guide to Information Security Testing and Assessment
- CWE-200: Exposure of Sensitive Information to an Unauthorized Actor
- CWE-942: Permissive Cross-domain Policy with Untrusted Domains
- RFC 6454 — The Web Origin Concept (CORS)
- GeoServer Security Documentation: https://docs.geoserver.org/stable/en/user/security/

---

*This is a fictionalized portfolio case study. No real infrastructure data, exploitation paths, or sensitive information is disclosed. Original responsible disclosure report delivered to the system owner. For questions, contact the repository maintainer.*
