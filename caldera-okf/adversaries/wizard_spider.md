---
type: Adversary Profile
title: Wizard Spider
description: Wizard Spider is a financially-motivated Russian cybercrime group known for TrickBot, Ryuk ransomware, and BazarLoader; the emulation profile covers 38 abilities across a multi-host scenario.
tags:
  - caldera
  - adversary
  - wizard-spider
  - trickbot
  - ryuk
  - ransomware
  - emu
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Wizard Spider

Wizard Spider is a financially-motivated Russian cybercrime group responsible for TrickBot, BazarLoader, and Ryuk ransomware campaigns targeting enterprises and healthcare organizations.

## Caldera Profile

| Field | Value |
|---|---|
| Adversary ID | `23ee980e-f0c5-4e88-a02c-56aa862b2c76` |
| Abilities | 38 |
| Recommended Planner | Wizard Spider Planner (`43f4e42d-5c93-43a7-89f4-227071014893`) |
| Fact Source ID | `68908278-cf73-5145-9fe9-ebff1c533b06` (Wizard Spider Emu — 11 seed facts) |
| Plugin | emu |

## Multi-Host Requirement

Wizard Spider requires **four agent groups**:

| Group | Role |
|---|---|
| `kali` | Attacker Linux host |
| `dorothy` | Initial victim workstation |
| `toto` | Lateral movement target |
| `wizard` | Domain controller or server |

## TTP Coverage

| Phase | Key Techniques |
|---|---|
| Initial Access | T1566 Phishing, T1078 Valid Accounts |
| Execution | T1059 Scripting, T1569 System Services |
| Persistence | T1547 Boot Autostart, T1053 Scheduled Task |
| Privilege Escalation | T1548 Abuse Elevation |
| Credential Access | T1003 OS Credential Dumping |
| Lateral Movement | T1021 Remote Services |
| Impact | T1486 Data Encrypted for Impact (ransomware simulation) |

## Launching the Operation

```json
POST /api/v2/operations
{
  "name": "Wizard Spider Emulation",
  "adversary": {"adversary_id": "23ee980e-f0c5-4e88-a02c-56aa862b2c76"},
  "group": "dorothy",
  "planner": {"id": "43f4e42d-5c93-43a7-89f4-227071014893"},
  "source": {"id": "68908278-cf73-5145-9fe9-ebff1c533b06"},
  "autonomous": 1
}
```
