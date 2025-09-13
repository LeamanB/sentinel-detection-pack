# Suspicious PowerShell Usage

**Goal:** Detect risky PowerShell execution patterns (encoded commands, hidden windows, bypass flags).

## Data Sources
- **Windows Security Event Log** (4688, 4104)
- Optional: **Defender for Endpoint** DeviceProcessEvents

## MITRE ATT&CK
- **Command and Scripting Interpreter: PowerShell (T1059.001)**

## Example
Word spawns PowerShell with `-nop -w hidden -enc ...` → **Alert**.
