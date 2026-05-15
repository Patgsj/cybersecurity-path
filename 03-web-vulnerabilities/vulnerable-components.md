# Vulnerable Components Analysis

## What are Vulnerable Components?
Using libraries, frameworks, or software with known vulnerabilities. The attacker does not need to find a new vulnerability — they just look up existing CVEs for the version in use.

## Real Case — Municipalidad de San Carlos
During passive reconnaissance, Nginx 1.26.1 was identified on the municipal server.

CVE search against nginx.org/en/security_advisories.html found 8 known vulnerabilities:

| CVE | Severity | Description |
|-----|----------|-------------|
| CVE-2026-27654 | Medium | Buffer overflow in ngx_http_dav_module |
| CVE-2026-27784 | Medium | Buffer overflow in ngx_http_mp4_module |
| CVE-2026-32647 | Medium | Buffer overflow in ngx_http_mp4_module |
| CVE-2026-27651 | Low | NULL pointer dereference CRAM-MD5 |
| CVE-2026-28753 | Medium | Injection in auth_http and XCLIENT |
| CVE-2025-23419 | Medium | SSL session reuse vulnerability |
| CVE-2025-53859 | Low | Buffer overread in ngx_mail_smtp_module |
| CVE-2024-7347 | Low | Buffer overread in ngx_http_mp4_module |

**Fix:** Update Nginx to 1.28.3+ to resolve all identified CVEs.

## Why Organizations Don't Update

- Fear of breaking existing functionality
- No dedicated IT security staff
- Budget constraints
- Bureaucratic processes
- "If it works, don't touch it" mentality

This is especially common in municipalities and public institutions.

## Famous Example — Log4Shell (CVE-2021-44228)
A vulnerability in Log4j, a Java logging library used in millions of systems.
- Severity: Critical (CVSS 10.0 — maximum)
- Affected: Amazon, Microsoft, Apple, Tesla, governments worldwide
- Attack: Remote code execution with a single line of text in a log field
- Cause: Outdated library with known vulnerability not patched

## How to Find Vulnerable Components
1. Identify software and versions (reconnaissance, nmap, DNSDumpster)
2. Search CVE databases (nvd.nist.gov, official vendor advisories)
3. Check if installed version falls within vulnerable range
4. Report and recommend update

## Defense
- Maintain inventory of all software components and versions
- Monitor CVE feeds for new vulnerabilities
- Establish regular update and patch management process
- Test updates in staging environment before production
- Remove unused components — less software = less attack surface

## Connection to Cadastral Security
A cadastral system running outdated software is a known target. Attackers scan for specific versions and apply known exploits — no creativity needed. The fix is simply keeping software updated.

## Key Insight
Vulnerable components turn public knowledge into exploitable attacks. The CVE database is public — both attackers and defenders use it. The difference is who acts on it first.
