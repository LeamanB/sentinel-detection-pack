# Sentinel Detection Pack

A compact, production-quality starter pack of Microsoft Sentinel detections focused on high-signal identity and execution abuse. Each detection includes clear logic, parameters, and MITRE ATT&CK mappings, plus docs with examples.

> **Goal:** Help teams stand up meaningful detections fast, then iterate. All queries are written for Microsoft Sentinel (KQL) and can be used as Hunting Queries or Analytics Rules.

---

## Detections

| Detection | Purpose | Data Source(s) | ATT&CK Mapping |
|---|---|---|---|
| Impossible travel logins | Flag accounts authenticating from geographically distant locations within an unrealistically short window (potential account takeover using stolen creds/VPN exit nodes). | `SigninLogs` | Valid Accounts (T1078) |
| Multiple failed logons followed by success | Identify likely brute-force/credential-stuffing where repeated failures are immediately followed by a success on the same account and IP/UA. | `SigninLogs` (Azure AD) or `SecurityEvent` (4625→4624) | Brute Force (T1110), Valid Accounts (T1078) |
| Suspicious PowerShell usage | Catch malicious PowerShell usage via process creation and/or Script Block Logging (encoded commands, execution policy bypass, hidden windows). | `SecurityEvent` (4688, 4104), optionally `DeviceProcessEvents` | Command & Scripting Interpreter: PowerShell (T1059.001) |

> **Note:** MITRE technique IDs are provided for mapping; tune and extend to your environment.

---

## Quick Start (Hunting)

1. Open **Microsoft Sentinel** → **Hunting** → **+ Add query**.
2. Paste one of the `.kql` files from `/detections/`.
3. Adjust **Parameters** section (time windows, thresholds, allowed corp IP ranges).
4. Run the query, review results, and validate with your environment.

---

## Convert to Analytics Rules

1. In **Analytics** → **+ Create** → **Scheduled query rule**.
2. Paste the KQL, set **Run frequency** and **Lookup period** to match the query’s default parameters (or your preference).
3. Set **Severity**, **Tactics/Techniques** (per table above), and **Entity mappings**:
   - **Account**: `AccountCustomEntity` or `AccountUPN`
   - **IP**: `IPCustomEntity`
   - **Host** (where applicable): `HostCustomEntity`
4. Add **Alert details** and **Response** (playbooks/automation).

---

## Repo Structure
/detections/ # KQL queries
/docs/ # Markdown docs for each detection
README.md # You are here


---

## Configuration & Tuning

- **Allow-list corporate egress**: Define or import a watchlist of trusted IP ranges and exclude them.
- **Service accounts**: Consider lower severity or separate rules for service principals.
- **Geo resolution**: This pack leverages `SigninLogs.LocationDetails`. If lat/long are unavailable, falls back to country/city changes.
- **Windows vs AAD**: The failed-then-success query supports both AAD `SigninLogs` and Windows `SecurityEvent` (4625/4624). Enable the path that matches your telemetry.

---

## Placeholders for Screenshots

Insert screenshots after you deploy and validate:

- **Hunting results view** (per detection)
- **Analytics rule configuration**
- **Alert details with entities**



---

## Contributing

- Fork, branch, PR. Add clear descriptions, test evidence, and ATT&CK mappings.
- Keep queries **performant** (summarize early, limit columns, use parameters).
- Provide docs: description, example logs, tuning tips, false-positive guidance.

---

## License

MIT
