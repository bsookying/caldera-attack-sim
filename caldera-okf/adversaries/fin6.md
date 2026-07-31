---
type: Adversary Profile
title: FIN6
description: FIN6 is a financially-motivated threat actor targeting point-of-sale environments and e-commerce platforms; the emulation profile covers 27 abilities.
tags:
  - caldera
  - adversary
  - fin6
  - financial
  - pos
  - emu
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# FIN6

FIN6 is a financially-motivated threat actor known for targeting point-of-sale (POS) systems in retail and hospitality, and later pivoting to ransomware deployment (LockerGoga, Ryuk) via Cobalt Strike.

## Caldera Profile

| Field | Value |
|---|---|
| Adversary ID | `123700e5-44c8-4894-a409-6484992c8846` |
| Abilities | 27 |
| Recommended Planner | atomic |
| Fact Source ID | `7cfe465a-9b3d-5a8f-8d65-10b5ba37a5d5` (FIN6 Emu — 30 seed facts) |
| Plugin | emu |

## Key TTPs

| Technique | Description |
|---|---|
| T1059.001 | PowerShell |
| T1003.001 | LSASS Credential Dumping |
| T1021.002 | SMB Lateral Movement |
| T1072 | Software Deployment Tools |
| T1074 | Data Staged |
| T1560.001 | Archive via Utility |
