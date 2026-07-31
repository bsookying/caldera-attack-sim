---
type: Adversary Profile
title: OilRig (APT34)
description: OilRig is an Iranian state-sponsored threat actor; the emulation profile requires a multi-host setup with attacker-controlled Linux (kali group) and target (gosta group), using a dedicated planner.
tags:
  - caldera
  - adversary
  - oilrig
  - apt34
  - iran
  - emu
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# OilRig (APT34)

OilRig (also APT34, HelixKitten) is an Iranian state-sponsored threat actor that targets Middle Eastern organizations in financial, government, energy, and telecom sectors.

## Caldera Profile

| Field | Value |
|---|---|
| Adversary ID | `db88ae87-8884-42f1-aa06-d7ef9b45721d` |
| Abilities | 44 |
| Recommended Planner | OilRig Planner (`0a7d8b12-c0fa-4aec-a890-368f3735f7a9`) |
| Fact Source ID | `fda0f88b-4cd7-5fb7-9424-ae23f61218be` (OilRig Emu — 13 seed facts) |
| Plugin | emu |

## Multi-Host Requirement

OilRig requires **two agent groups**:

| Group | Host | Role |
|---|---|---|
| `kali` | Attacker-controlled Linux | Runs attacker-originating commands |
| `gosta` | Target host | Runs victim-side commands |

The OilRig Planner coordinates commands between these groups to simulate realistic attacker/victim interaction.

## TTP Coverage

| Phase | Key Techniques |
|---|---|
| Initial Access | T1566.001 Spearphishing Attachment |
| Execution | T1059.001 PowerShell, T1059.006 Python |
| Persistence | T1505.003 Web Shell |
| Credential Access | T1003.001 LSASS Dump |
| Discovery | T1016 Network Config, T1033 System Owner |
| Lateral Movement | T1021.002 SMB |
| Exfiltration | T1567 Web Service |

## Launching the Operation

```json
POST /api/v2/operations
{
  "name": "OilRig Emulation",
  "adversary": {"adversary_id": "db88ae87-8884-42f1-aa06-d7ef9b45721d"},
  "group": "gosta",
  "planner": {"id": "0a7d8b12-c0fa-4aec-a890-368f3735f7a9"},
  "source": {"id": "fda0f88b-4cd7-5fb7-9424-ae23f61218be"},
  "autonomous": 1
}
```

**Note:** Ensure agents are running in both `kali` and `gosta` groups before starting.
