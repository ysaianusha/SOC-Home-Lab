# Windows Security Event Investigation

## 1. Objective

Investigate Windows Security Event IDs 4624, 4625 and 4634 to understand successful authentication, failed authentication and logoff activities.

## 2. Environment

- Operating System: Windows
- Tool: Windows Event Viewer
- Log Source: Windows Security Logs

## 3. Event Analysis – Successful Logon

**Event ID:** 4624

- Date: 02-Nov-2026
- Logon Type: 5 – Service Logon
- Account: Computer account (identifier redacted)
- Source Network Address: Not available

**Observation:**

A successful service logon was recorded. Logon Type 5 indicates service-based authentication.

This event is not inherently suspicious.

## 4. Event Analysis – Failed Logon

**Event ID:** 4625

- Date: 27-Oct-2026
- Account: Local account (identifier redacted)
- Logon Type: 2 – Interactive
- Status: 0xC000006D – Generic logon failure
- SubStatus: 0xC000006A – Incorrect password
- Logon Process: Advapi
- Authentication Package: Negotiate
- Workstation Name: Not available

**Observation:**

A failed interactive authentication attempt was recorded. The SubStatus indicates that the supplied password was incorrect.

## 5. Security Analysis

A single failed authentication event does not establish a brute-force attack.

Further investigation would require:
- Reviewing repeated failed login attempts.
- Identifying targeted accounts.
- Examining source IP addresses where available.
- Correlating failed and successful authentication events.
- Investigating unusual login patterns.

## 6. Security Impact

Repeated unauthorized authentication attempts could potentially threaten:

- Confidentiality through unauthorized data access.
- Integrity through unauthorized data modification.
- Availability through account lockouts or service disruption.

## 7. Conclusion

The investigation identified both successful service authentication and a failed interactive authentication event.

The available evidence does not establish malicious activity or a confirmed security incident.

Additional log correlation and contextual analysis would be required.

## 8. Skills Practiced

- Windows Event Viewer
- Windows Security Event IDs
- Authentication failure analysis
- Logon type identification
- Status and SubStatus interpretation
- Initial SOC investigation and documentation
