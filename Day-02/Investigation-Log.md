# Windows Account Creation & Privilege Escalation Investigation

## 1. Objective

Investigate Windows Security Event Logs generated when a local user account is created and added to the local Administrators group.

The exercise simulates a controlled account-management scenario to practise detecting potential unauthorized account creation and privilege assignment. A new local account being added to an administrator group can be security-sensitive, but the activity must be validated against authorization and business context before it is treated as malicious.

## 2. Environment

- Operating System: Windows 11
- Tools: Windows Event Viewer, PowerShell, Auditpol
- Log Source: Windows Logs → Security
- Test Account: `LabAuditUser`
- Target Group: Local `Administrators` group
- Environment: Personal / authorized lab

## 3. Audit Policy Configuration

Checked the current audit policy configuration:

```powershell
auditpol /get /subcategory:"User Account Management"

auditpol /get /subcategory:"Security Group Management"
```

Enabled successful auditing for the relevant categories:

```powershell
auditpol /set /subcategory:"User Account Management" /success:enable

auditpol /set /subcategory:"Security Group Management" /success:enable
```

These settings allow Windows to record account-management and security-group-management activity. Audit policy settings can be controlled by local policy or domain policy; on managed systems, the effective policy may be controlled centrally.

## 4. Controlled Test Activity

Created a temporary local account:

```powershell
net user LabAuditUser * /add
```

Added the test account to the local Administrators group:

```powershell
net localgroup Administrators LabAuditUser /add
```

Verified the account and group membership:

```powershell
net user LabAuditUser

net localgroup Administrators
```

This was a deliberate lab action, not an attempt to access or modify an unauthorized system.

## 5. Event Viewer Investigation

Opened Event Viewer using `eventvwr.msc` and navigated to:

**Windows Logs → Security → Filter Current Log**

Filtered for the following event IDs:

```text
4720, 4732
```

### Event ID 4720 — A user account was created

The event is generated when a user account is created.

Fields to inspect:

- **Subject:** Account that performed the creation operation.
- **New Account → Account Name:** Account that was created.
- **New Account → Security ID:** SID assigned to the new account.
- **Time Created:** When the event was recorded.
- **Computer:** System on which the event was recorded.

### Event ID 4732 — A member was added to a security-enabled local group

The event is generated when a member is added to a security-enabled local group.

Fields to inspect:

- **Subject:** Account that requested the group-membership change.
- **Member → Security ID / Account Name:** Account added to the group.
- **Group → Group Name:** Local group that received the member.
- **Group → Security ID:** SID of the destination group.
- **Time Created:** When the membership change was recorded.

For this lab, check whether the group is the local **Administrators** group.

## 6. Event Correlation

The investigation should establish whether the account created in Event 4720 is the same account added in Event 4732.

Correlation checks:

1. Compare the **New Account Security ID** in Event 4720 with the **Member Security ID** in Event 4732.
2. Compare timestamps to establish the order of activity.
3. Review the **Subject** fields in both events to identify the account responsible for each operation.
4. Compare the Logon ID values where available to help link related activity performed in the same logon session.
5. Confirm the destination group in Event 4732 is the local Administrators group.

A matching SID is stronger evidence of account identity than matching display names alone. A sequence of account creation followed by group membership is suspicious when unexpected, but the events alone do not prove malicious intent.

## 7. PowerShell Validation

Queried the Security log for the account-management and group-membership events:

```powershell
$start = (Get-Date).AddHours(-2)

Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    Id = 4720,4732,4733,4726
    StartTime = $start
} |
Select-Object TimeCreated, Id, MachineName, Message |
Format-List
```

Exported the returned events for review:

```powershell
$start = (Get-Date).AddHours(-2)

Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    Id = 4720,4732,4733,4726
    StartTime = $start
} |
Select-Object TimeCreated, Id, MachineName, Message |
Export-Csv "$env:USERPROFILE\Desktop\Account-Admin-Investigation.csv" -NoTypeInformation
```

The two-hour query window is relative to the time the command is run. If the exercise took place earlier, adjust `$start` and rerun the query so that the relevant events fall inside the time range.

## 8. Cleanup Verification

Removed the temporary user from the local Administrators group:

```powershell
net localgroup Administrators LabAuditUser /delete
```

Deleted the temporary user:

```powershell
net user LabAuditUser /delete
```

Relevant cleanup events:

| Event ID | Description |
|---|---|
| 4733 | A member was removed from a security-enabled local group |
| 4726 | A user account was deleted |

The cleanup events should be checked in the Security log. Do not assume cleanup succeeded solely because the commands were entered; verify the account and group state and inspect the relevant events.

## 9. Investigation Findings

Complete this table using the values shown in your own Event Viewer records. Do not include passwords, sensitive account details, or unredacted personal information.

| Investigation item | Observed result |
|---|---|
| Event 4720 present | [Record whether found] |
| Account created | [Enter observed account name] |
| Event 4720 timestamp | [Enter observed timestamp] |
| Subject that created the account | [Enter observed subject] |
| New account SID | [Enter redacted SID or note that it was verified] |
| Event 4732 present | [Record whether found] |
| Member added to the group | [Enter observed member] |
| Destination group | [Enter observed group] |
| Subject that added the member | [Enter observed subject] |
| SID correlation | [Explain whether the SIDs matched] |
| Event 4733 cleanup event | [Record whether found] |
| Event 4726 cleanup event | [Record whether found] |
| Final account/group state | [Confirm the test account was removed] |

### Screenshots / Evidence to add

Place sanitized screenshots in this folder and link them here when added:

- Audit policy output
- Event ID 4720 details
- Event ID 4732 details
- PowerShell query output
- Event ID 4733 and 4726 cleanup evidence

## 10. Security Analysis

Unexpected creation of local accounts and additions to privileged groups may indicate unauthorized persistence or privilege escalation.

Recommended SOC investigation steps:

- Confirm whether the account creation was approved.
- Identify the account responsible for creating the user and changing group membership.
- Verify whether the target group is privileged.
- Correlate account-management events with logon events, such as Event ID 4624, using timestamps and Logon ID where available.
- Review other activity performed by the account after it received administrator membership.
- Escalate according to the organization's incident-response process if the activity is unauthorized or cannot be explained.

## 11. Conclusion

This lab practises monitoring Windows account-management and local security-group changes using native Windows auditing, Event Viewer, and PowerShell.

The main detection pair is Event ID 4720 (account creation) followed by Event ID 4732 (addition to a security-enabled local group). SID, Subject, Logon ID, group name, and timestamp correlation help reconstruct the sequence of activity.

The presence of these events is not, by itself, proof of a security incident. The analyst must validate authorization, context, and subsequent activity before reaching a conclusion.

## 12. Skills Practised

- Windows Security Event Log monitoring
- Audit policy configuration with Auditpol
- Local account-management investigation
- Privileged group membership monitoring
- Event ID 4720 and 4732 analysis
- SID and Logon ID correlation
- PowerShell log querying and CSV export
- Cleanup verification
- SOC investigation and documentation

## 13. References

- [Microsoft Learn — Event ID 4720: A user account was created](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4720)
- [Microsoft Learn — Event ID 4732: A member was added to a security-enabled local group](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/event-4732)
- [Microsoft Learn — Advanced Audit Policy Configuration](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/advanced-audit-policy-configuration)

## Disclaimer

All practical exercises are performed in personal or authorized laboratory environments. No unauthorized systems are targeted. Sensitive values in screenshots and exported logs must be redacted before publication.
