---
type: Adversary Profile
title: Sandworm Team (G0034)
description: Sandworm is a Russian GRU-attributed threat actor responsible for NotPetya and attacks on Ukrainian infrastructure; the emulation profile covers 40 abilities in a multi-host scenario.
tags:
  - caldera
  - adversary
  - sandworm
  - gru
  - russia
  - notpetya
  - emu
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Sandworm Team (G0034)

Sandworm (Voodoo Bear, BlackEnergy) is a Russian GRU Unit 74455 threat actor known for destructive attacks including NotPetya, Ukrainian power grid attacks, and Olympic Destroyer.

## Caldera Profile

| Field | Value |
|---|---|
| Adversary ID | `c397ca4d-3c68-414e-9f78-2a96a11edbef` |
| Abilities | 40 |
| Recommended Planner | Sandworm Planner (`6f46e3a4-f20e-49ed-8088-32a3094aad77`) |
| Fact Source ID | `ec9d08d5-a574-5d8f-9697-bba9b4f033f7` (Sandworm Emu — 9 seed facts) |
| Plugin | emu |

## Multi-Host Requirement

| Group | Role |
|---|---|
| `kali` | Attacker Linux host |
| `arrakis` | Initial target |
| `caladan` | Spawned during operation |
| `gammu` | Spawned during operation |

## TTP Coverage

| Phase | Key Techniques |
|---|---|
| Initial Access | T1190 Exploit Public-Facing App, T1566 Phishing |
| Execution | T1059 Scripting, T1072 Software Deployment Tools |
| Persistence | T1543 Create/Modify System Process |
| Defense Evasion | T1027 Obfuscation, T1070 Indicator Removal |
| Discovery | T1016 Network Config, T1082 System Info |
| Lateral Movement | T1021.002 SMB/Windows Admin Shares |
| Impact | T1485 Data Destruction, T1529 System Shutdown |
