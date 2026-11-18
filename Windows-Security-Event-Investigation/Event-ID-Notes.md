# Windows Security Event IDs

## Event ID 4624 – Successful Logon
Indicates that a user or account successfully logged on.

## Event ID 4625 – Failed Logon
Indicates that an authentication attempt failed.

## Event ID 4634 – Account Logoff
Indicates that a logon session was terminated.

## Logon Types

| Logon Type | Description |
|---|---|
| 2 | Interactive logon |
| 5 | Service logon |

## Authentication Failure Codes

| Code | Meaning |
|---|---|
| 0xC000006D | Generic logon failure |
| 0xC000006A | Incorrect password |
| 0xC0000064 | User account does not exist |

## Key Learning
A single failed authentication event does not establish a brute-force attack. Multiple events and contextual evidence must be investigated.
