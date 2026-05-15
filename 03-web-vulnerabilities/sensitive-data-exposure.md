# Sensitive Data Exposure Analysis

## What is Sensitive Data Exposure?
Sensitive Data Exposure occurs when a system stores or transmits critical information without adequate protection. It is not an attack itself — it is a misconfiguration that enables all other attacks.

## Three Types Identified in Practice

### 1. Data Exposed in Transmission — FTP
During reconnaissance of munisancarlos.cl, port 21 (FTP) was found open.

FTP transmits everything in plain text — including admin credentials.
Anyone on the same network with Wireshark can capture:
- Username
- Password
- All files transferred

**Defense:** Replace FTP with SFTP (encrypted file transfer)

### 2. Software Version Exposed
During passive reconnaissance with DNSDumpster:
Server: nginx/1.26.1

A well-configured server hides the version number, showing only "nginx".
With the exact version, an attacker can search CVEs and find known vulnerabilities.
This is exactly how 8 CVEs were identified for munisancarlos.cl.

**Defense:** Configure server to hide version information
```nginx
server_tokens off;
```

### 3. Verbose Error Messages
Systems that reveal technical details in error messages expose:
- Server file paths: `C:\inetpub\wwwroot\bank\login.jsp`
- Software versions: `Apache Tomcat 9.0.x`
- Database structure: `SQLException: column 'password' not found`

**Defense:** Show generic error messages to users, log technical details server-side only

## Sensitive Data That Must Always Be Protected
- Passwords — always hashed, never plain text
- Credit card numbers — encrypted at rest and in transit
- National ID numbers / RUT
- Land registry data and property owner information
- Session tokens and API keys

## Connection to Cadastral Security
A cadastral system exposing:
- Software versions → attacker finds CVEs
- FTP for file transfer → attacker captures admin credentials
- Plain text passwords in database → one breach exposes all users

## Key Insight
Sensitive Data Exposure is often invisible to users but catastrophic when exploited. The data was always there — it just wasn't protected. Defense requires thinking about every place data is stored, transmitted, or displayed.
