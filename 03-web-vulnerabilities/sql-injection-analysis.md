# SQL Injection Analysis

## What is SQL Injection?
SQL Injection occurs when an attacker inserts malicious SQL code into an input field, manipulating the database query logic instead of providing normal data.

## How it Works

Normal login query:
```sql
SELECT * FROM users WHERE user='admin' AND password='mypassword'
```

Injected query:
```sql
SELECT * FROM users WHERE user='admin' AND password='' OR '1'='1'
```

`'1'='1'` is always true — the system grants access regardless of the password.

## Practical Demonstration

**Target:** Altoro Mutual Demo Bank (demo.testfire.net) — legal practice site

**Attack performed:**
- Username: `admin`
- Password: `' OR '1'='1`

**Result:** Full admin access granted without valid credentials

**Access obtained:**
- Account balances and transaction history
- Ability to change any user's password
- Ability to create new users
- Full administrative control

## Impact on a Cadastral System
If a land registry system is vulnerable to SQL injection:
- Attacker reads all land ownership records
- Modifies property boundaries or owners
- Deletes registry records
- Creates admin accounts
- Locks out legitimate administrators

## Defense — Prepared Statements
Instead of building queries with user input directly:
```sql
"SELECT * FROM users WHERE password = '" + user_input + "'"
```

Use parameterized queries:
```sql
SELECT * FROM users WHERE password = ?
```

The `?` placeholder ensures user input is always treated as a string — never as executable code. Even `' OR '1'='1` becomes a literal string that matches no password.

## Key Insight
SQL Injection requires no special tools — just knowledge of SQL and a vulnerable input field. It is consistently ranked #1 or #3 in the OWASP Top 10 because it is simple to execute and devastating in impact.

## Connection to Cadastral Security
A cadastral system search field (searching by land roll number) that builds SQL queries without parameterization exposes the entire land registry database to any user who knows basic SQL.
