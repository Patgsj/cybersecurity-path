# Broken Access Control Analysis

## What is Broken Access Control?
Broken Access Control occurs when a system fails to properly verify whether a user has permission to access a specific resource. It is #1 in the OWASP Top 10.

## How it Works
The system checks permissions at login but fails to verify them on every individual page or resource request.

**Normal flow (secure):**
1. User logs in → system verifies role
2. User requests a page → system verifies role again
3. If no permission → redirect to login

**Broken Access Control:**
1. User logs in → system verifies role
2. User manually types a restricted URL → system does NOT verify role
3. User accesses restricted content directly

## IDOR — Insecure Direct Object Reference
The most common type of Broken Access Control.

Example — a banking system shows account details via URL:
/bank/showAccount?acctId=1001160140

An attacker simply changes the number:
/bank/showAccount?acctId=1001160141

If the system doesn't verify that this account belongs to the logged-in user — the attacker sees another user's account.

## Impact on a Cadastral System
A cadastral search URL like:
catastro.munisancarlos.cl/predio?rol=123-456

If vulnerable to IDOR:
- Any logged-in user can view any land record by changing the rol number
- Any user can modify records belonging to others
- No admin access needed — just a valid login

## Real Example
During SQL Injection practice on demo.testfire.net, after gaining admin access via SQL Injection, the system had no additional access controls — any authenticated user could navigate to admin pages directly.

## Defense
- Verify permissions on **every request**, not just at login
- Never trust that the user arrived through the correct flow
- Check that the requested resource belongs to the authenticated user
- Implement server-side authorization checks — never rely on client-side only

## Connection to Personal Experience
This defense was already implemented correctly in a personal SaaS project — users without the correct session are redirected to login regardless of the URL they type. This is the correct implementation of access control middleware.

## Key Insight
Broken Access Control is #1 in OWASP Top 10 because it is extremely common and easy to exploit — just change a number in a URL. The fix requires discipline: verify permissions everywhere, always.
