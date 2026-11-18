# 🔐 SOC Home Lab – Cybersecurity Portfolio

## About This Repository

This repository documents my hands-on cybersecurity learning journey, focusing on Security Operations Center (SOC) investigations, Windows security event analysis, threat detection, vulnerability assessment, and incident response.

The objective is to develop practical skills through controlled laboratory exercises and document investigation findings using industry-relevant tools and methodologies.

## 🎯 Learning Objectives

- Understand Windows Security Event Logs.
- Investigate successful and failed authentication events.
- Identify suspicious authentication patterns.
- Monitor local account creation and privileged group membership changes.
- Correlate Windows security events using timestamps, SIDs, and Logon IDs.
- Develop foundational SIEM investigation skills.
- Practice network traffic analysis.
- Learn vulnerability assessment and incident documentation.

## 🛠️ Tools and Technologies

| Tool | Purpose |
|---|---|
| Windows Event Viewer | Security log investigation |
| PowerShell | Event querying and investigation validation |
| Auditpol | Windows audit policy inspection and configuration |
| Wireshark | Network traffic analysis |
| Nmap | Network discovery and scanning |
| Microsoft Sentinel | SIEM fundamentals |
| MITRE ATT&CK | Threat behavior mapping |
| GitHub | Project documentation |

## 📂 Project Structure

### Windows Security Event Investigation

Investigated Windows Security Event IDs 4624, 4625 and 4634 to understand successful authentication, failed authentication and logoff activities.

Areas covered:

- Identified successful and failed authentication events.
- Analyzed Logon Types.
- Interpreted authentication Status and SubStatus codes.
- Considered what additional evidence is needed before classifying repeated failed logins as a brute-force attempt.
- Documented investigation findings.

**Documentation:** [Investigation Log](Windows-Security-Event-Investigation/Investigation-Log.md)

**Event Reference:** [Windows Event ID Notes](Windows-Security-Event-Investigation/Event-ID-Notes.md)

### Account Creation & Privilege Escalation Monitoring

Performed a controlled Windows lab exercise to monitor local account creation and addition of an account to the local Administrators group.

Areas covered:

- Inspected and enabled successful auditing for User Account Management and Security Group Management.
- Created a temporary local lab account.
- Added the test account to the local Administrators group.
- Investigated Event ID 4720 (user account created) and Event ID 4732 (member added to a security-enabled local group).
- Practised correlating Subject, Member, group, SID, Logon ID, and timestamp fields.
- Queried relevant Security events using PowerShell.
- Removed the temporary account and documented cleanup verification events (4733 and 4726).

**Documentation:** [Investigation Log](Account-Creation-Privilege-Escalation-Monitoring/Investigation-Log.md)

**Event Reference:** [Account Management Event ID Reference](Account-Creation-Privilege-Escalation-Monitoring/Event-ID-Reference.md)

> **Lab note:** The account-management exercise is a controlled simulation. The presence of account-creation or group-membership events is not, by itself, proof of malicious activity. Authorization and surrounding context must be investigated. Exact findings should be recorded from the lab's own event evidence.

## 📈 Skills Being Developed

- Security Monitoring
- Windows Event Log Analysis
- Authentication Investigation
- Account Management Monitoring
- Privileged Group Membership Investigation
- PowerShell Log Querying
- Event Correlation
- Threat Detection
- Incident Analysis
- Vulnerability Assessment
- Security Documentation

## ⚠️ Disclaimer

All practical exercises are performed in personal or authorized laboratory environments. No unauthorized systems are targeted. Screenshots and exported logs must be reviewed and sensitive information redacted before publication.

## 🚀 Ongoing Development

This repository will be updated as I complete additional hands-on exercises, security investigations, and practical cybersecurity projects.
