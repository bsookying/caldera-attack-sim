---
type: Adversary Profile
title: Carbanak
description: Carbanak is a financially-motivated threat actor targeting financial institutions; the emulation covers 24 abilities focused on banking and SWIFT fraud TTPs.
tags:
  - caldera
  - adversary
  - carbanak
  - fin7
  - financial
  - emu
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Carbanak

Carbanak is a financially-motivated cybercrime group (overlapping with FIN7) responsible for hundreds of millions in losses from banks globally via ATM jackpotting and SWIFT manipulation.

## Caldera Profile

| Field | Value |
|---|---|
| Adversary ID | `186b4d47-f4c0-48cb-9688-887070db45f1` |
| Abilities | 24 |
| Recommended Planner | atomic |
| Fact Source ID | `6a6bf917-a864-546f-b291-3419d642d67a` (Carbanak Emu — 11 seed facts) |
| Plugin | emu |

## Key TTPs

| Technique | Description |
|---|---|
| T1566.001 | Spearphishing with macro |
| T1059.001 | PowerShell execution |
| T1071.001 | HTTP C2 |
| T1021 | Remote Services lateral movement |
| T1005 | Local data collection |
| T1041 | C2 exfiltration |
