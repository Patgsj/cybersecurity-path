# Passive Reconnaissance — Municipalidad de San Carlos

## Objective
Demonstrate passive reconnaissance techniques against a real target using only public information. No systems were touched or accessed without authorization.

## Target
- **Organization:** Municipalidad de San Carlos, Ñuble, Chile
- **Domain:** munisancarlos.cl
- **Type:** Public municipal institution

## Tools Used
- WHOIS lookup (whois.com)
- DNS Dumpster (dnsdumpster.com)

## Findings

### WHOIS Analysis
| Field | Value |
|-------|-------|
| Registrant | Municipalidad de San Carlos |
| Registrar | NIC Chile |
| Created | 2018-05-24 |
| Expires | 2026-05-24 |
| Name Servers | ns1/ns2.server06.xhost.cl |

**⚠️ Risk:** Domain expires in less than 30 days. If not renewed, an attacker could register it and impersonate the municipality.

### Infrastructure Discovered
| Subdomain | IP | Service |
|-----------|-----|---------|
| www.munisancarlos.cl | 192.141.168.36 | Nginx 1.26.1 |
| webmail.munisancarlos.cl | 192.141.168.36 | Webmail |
| cpanel.munisancarlos.cl | 192.141.168.36 | cPanel Admin |

**⚠️ Critical Risk:** cPanel admin panel is publicly accessible. This is the server control panel — if compromised, attacker gains full control of hosting.

### Technology Stack
- Web server: Nginx 1.26.1
- Hosting provider: xhost.cl (individual provider)
- Server: server06.xhost.cl (shared hosting)

**⚠️ Risk:** Shared hosting means other clients share the same server. If another client is compromised, it could affect the municipality.

## Vulnerability Summary

| Vulnerability | Risk Level | Recommendation |
|--------------|------------|----------------|
| Domain expiring soon | High | Renew immediately |
| cPanel publicly exposed | Critical | Restrict access by IP |
| Shared hosting | Medium | Consider dedicated server |
| Nginx version exposed | Low | Hide version in headers |

## Key Insight
All this information was gathered in under 10 minutes using only public tools — without touching any system or leaving any trace in server logs. This is what an attacker does before any active attack.

## Legal Note
This reconnaissance was performed for educational purposes only using publicly available information. No systems were accessed without authorization.
