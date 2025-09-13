# Multiple Failed Logons Followed by Success

**Goal:** Identify brute-force or credential-stuffing bursts where repeated failures are followed by a successful sign-in for the same account from the same IP (and optionally the same User Agent).

---

## Data Sources

- **Azure AD Sign-in Logs** (`SigninLogs`)
- **Windows Security Event Log** (`SecurityEvent` 4625 → 4624)

---

## Logic

- Count failed logons per `(user, IP [, UA])` in rolling **15-minute** windows.  
- Trigger when **≥ 5** failures occur and a **success** follows within **10 minutes** after the last failure.  
- IP allowlisting supported to exclude corporate VPN or known service IPs.

---

## MITRE ATT&CK Mapping

- **Brute Force (T1110)**  
- **Valid Accounts (T1078)**

---

## Tuning Guidance

- **`minFailures`**: Raise to 8–10 in noisy environments.  
- **`sameUserAgent`**: Enable if you want higher fidelity in Azure AD.  
- **Service accounts**: Consider separate thresholds and alerts; they often fail on legitimate retries.  
- **Noise reduction**: Exclude known VPN concentrators, monitoring tools, or lab environments.

---

## Example Logs

**AAD Failed Sign-in (SigninLogs):**
```json
{
  "TimeGenerated": "2025-09-12T01:10:03Z",
  "UserPrincipalName": "jordan.lee@contoso.com",
  "IPAddress": "203.0.113.77",
  "Status": {
    "errorCode": 50053,
    "failureReason": "Account is locked because user tried to sign in too many times with an incorrect user ID or password."
  }
}
```

(repeated 4+ events)

**AAD Successful Sign-in:**
```json
{
  "TimeGenerated": "2025-09-12T01:18:16Z",
  "UserPrincipalName": "jordan.lee@contoso.com",
  "IPAddress": "203.0.113.77",
  "Status": { "errorCode": 0 }
}
```

---

## Outcome

≥ 5 failures followed by success within 10 minutes ⇒ **Alert**

---

## Response Actions

- Reset the affected user’s password and revoke active sessions.  
- Investigate whether the IP attempted logons against other accounts (spray pattern).  
- Consider Conditional Access policies or MFA enforcement for risky logins.  
- Block the attacking IP if confirmed malicious.
