---
type: Adversary Profile
title: APT29 (Cozy Bear)
description: APT29 is a Russian state-sponsored threat actor attributed to SVR; emulation profile covers 79 abilities across initial access, persistence, credential access, lateral movement, collection, and exfiltration.
tags:
  - caldera
  - adversary
  - apt29
  - cozy-bear
  - russia
  - svr
  - emu
sources:
  - id: ctid-apt29
    resource: https://github.com/center-for-threat-informed-defense/adversary_emulation_library/tree/master/apt29
    title: CTID APT29 Emulation Plan
    author: team:ctid
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# APT29 (Cozy Bear)

APT29 (also known as Cozy Bear, The Dukes) is a sophisticated Russian state-sponsored threat actor attributed to the SVR (Foreign Intelligence Service). The CTID emulation plan replicates their documented TTPs from multiple campaigns.[^ctid-apt29]

[^ctid-apt29]: CTID APT29 Emulation Plan

## Caldera Profile

| Field | Value |
|---|---|
| Adversary ID | `4975696e-1d41-11eb-adc1-0242ac120002` |
| Abilities | 79 |
| Recommended Planner | atomic |
| Fact Source ID | `152ca62e-32b6-5c2a-84e8-89fedd44d621` (APT29 Emu — 18 seed facts) |
| Plugin | emu |

## TTP Coverage

APT29's emulation spans the full kill chain:

| Phase | Key Techniques |
|---|---|
| Initial Access | T1566.002 Spearphishing Link, T1189 Drive-by |
| Execution | T1059.001 PowerShell, T1059.003 cmd |
| Persistence | T1547.001 Registry Run Keys, T1053.005 Scheduled Task |
| Privilege Escalation | T1548.002 UAC Bypass |
| Defense Evasion | T1027 Obfuscated Files, T1070 Indicator Removal |
| Credential Access | T1003.001 LSASS Memory, T1552 Unsecured Credentials |
| Discovery | T1057 Process Discovery, T1049 Network Connections, T1082 System Info |
| Lateral Movement | T1021.001 RDP, T1021.006 WinRM |
| Collection | T1005 Local Data, T1039 Network Share Data |
| C2 | T1071.001 Web Protocols, T1132 Data Encoding |
| Exfiltration | T1041 C2 Channel |

## Launching the Operation

```json
POST /api/v2/operations
{
  "name": "APT29 Emulation",
  "adversary": {"adversary_id": "4975696e-1d41-11eb-adc1-0242ac120002"},
  "group": "red",
  "planner": {"id": "aaa7c857-37a0-4c4a-85f7-4e9f7f30e31a"},
  "source": {"id": "152ca62e-32b6-5c2a-84e8-89fedd44d621"},
  "obfuscator": "base64",
  "autonomous": 1,
  "jitter": "5/15"
}
```
