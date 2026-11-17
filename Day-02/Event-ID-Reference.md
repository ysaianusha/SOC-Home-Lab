# Windows Account Management & Group Membership Event Reference

This reference supports **Day 02 — Account Creation & Privilege Escalation Investigation**.

## Key Event IDs

| Event ID | Event description | Investigation use |
|---|---|---|
| 4720 | A user account was created | Identify the account creation action and the account created |
| 4732 | A member was added to a security-enabled local group | Identify the account added, the destination group, and the actor |
| 4733 | A member was removed from a security-enabled local group | Verify removal from a local group during cleanup |
| 4726 | A user account was deleted | Verify deletion of a test account during cleanup |
| 4624 | An account was successfully logged on | Correlate logon activity with account-management events where useful |

## Important Fields

### Event ID 4720 — User account created

- **Subject:** The security principal that performed the account-creation operation.
- **New Account:** The account that was created, including its name and Security ID (SID).
- **Time Created:** Timestamp for the event.
- **Computer:** Computer where the event was recorded.

### Event ID 4732 — Member added to a security-enabled local group

- **Subject:** The account that requested the membership change.
- **Member:** The account added to the group, including its SID and account name where available.
- **Group:** The destination group, including its group SID and group name.
- **Logon ID:** Can help correlate the activity with other events from the same logon session.
- **Time Created:** Timestamp for the event.

## Correlation Tips

1. Match the **New Account SID** from Event 4720 with the **Member SID** from Event 4732.
2. Compare timestamps to check the sequence of actions.
3. Review Subject identities to identify who performed each operation.
4. Compare Logon IDs when present to help link related activity.
5. Confirm whether the destination group is privileged, such as the local Administrators group.

Do not treat a new account or group-membership event as automatically malicious. Validate whether the action was authorized and investigate the surrounding activity.

## Audit Policy Categories

| Audit policy subcategory | What it helps record |
|---|---|
| User Account Management | Creation, modification, deletion, enabling, disabling, and other user-account management activities |
| Security Group Management | Addition or removal of members from security-enabled groups and related group-management activity |

For the local group exercise in this lab, Event ID 4732 is the key group-addition event. Event ID 4728 applies to a security-enabled global group, while Event ID 4756 applies to a security-enabled universal group.

## Quick PowerShell Queries

Check the current audit policy:

```powershell
auditpol /get /subcategory:"User Account Management"
auditpol /get /subcategory:"Security Group Management"
```

Query account creation, group membership, and cleanup events from the last two hours:

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

Change the time range if the lab activity happened earlier.

## Microsoft References

- [Event ID 4720 — A user account was created](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4720)
- [Event ID 4732 — A member was added to a security-enabled local group](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/auditing/event-4732)
- [Advanced Audit Policy Configuration](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/advanced-audit-policy-configuration)
