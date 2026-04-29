# Windows Security Event Logs Analysis

## What are Event Logs?
Windows records every security-relevant action in the Security Event Log. Each event has a unique ID that identifies what happened, when, and who triggered it.

## Key Event IDs for SOC Analysts

| Event ID | Description | Risk Level |
|----------|-------------|------------|
| 4624 | Successful logon | Monitor for unexpected sources |
| 4625 | Failed logon | High — indicates brute force if repeated |
| 4634 | Logoff | Normal |
| 4648 | Logon with explicit credentials | Medium |
| 4720 | New user account created | High |
| 4732 | User added to privileged group | Critical |
| 4688 | New process created | Monitor for suspicious processes |
| 5379 | Credential Manager credentials read | Medium |

## Tools Used

**GUI:** Windows Event Viewer (`eventvwr`)  
**CLI:** `wevtutil` — faster, scriptable, used by real analysts

## CLI Commands Used

```bash
# View last 10 security events
wevtutil qe Security /c:10 /rd:true /f:text

# Filter by specific Event ID (example: failed logons)
wevtutil qe Security /q:"*[System[EventID=4625]]" /c:5 /rd:true /f:text

# Filter successful logons
wevtutil qe Security /q:"*[System[EventID=4624]]" /c:5 /rd:true /f:text
```
net use \192.168.0.106\c$ /user:usuariofalso contraseñafalsa
## Real Analysis — Failed Logon Detection

Generated a failed logon attempt using: Found in logs:
- Event ID: 4625
- Account: usuariofalso
- Source IP: 192.168.0.106
- Reason: Unknown username or bad password

## Brute Force Attack Signature

A brute force attack appears in logs as:
- Multiple 4625 events from the same IP in seconds
- Followed by a 4624 from the same IP = successful entry

## Connection to Cadastral Security
A cadastral system exposed to the network without log monitoring would not detect brute force attempts against its database credentials. Monitoring 4625 events is the first line of detection.
