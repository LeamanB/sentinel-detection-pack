# Impossible Travel Logins

**Goal:** Detect accounts authenticating from two distant locations within an unrealistically short period. This is often a sign of credential compromise via VPN/proxy abuse.

## Data Source
- **Azure AD Sign-in logs** (`SigninLogs`)

## MITRE ATT&CK
- **Valid Accounts (T1078)**

## Example
User logs in from Chicago, then 62 minutes later from Warsaw (~7,400 km). → **Alert**.
