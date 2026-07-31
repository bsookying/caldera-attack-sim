---
type: Adversary Profile
title: menuPass (APT10)
description: menuPass (APT10, Stone Panda) is a Chinese state-sponsored threat actor targeting managed service providers; the emulation covers 15 abilities with 45 seed facts.
tags:
  - caldera
  - adversary
  - menupass
  - apt10
  - china
  - msp
  - emu
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# menuPass (APT10)

menuPass (also APT10, Stone Panda, CVNX) is a Chinese state-sponsored threat actor attributed to the MSS (Ministry of State Security). Known for targeting managed service providers (MSPs) to compromise downstream clients at scale.

## Caldera Profile

| Field | Value |
|---|---|
| Adversary ID | `b5192070-dff1-4134-bf66-c6b87c7498af` |
| Abilities | 15 |
| Recommended Planner | atomic |
| Fact Source ID | `a66d2d89-ce1f-50d1-8730-a79e45a1b5a2` (menuPass Emu — 45 seed facts) |
| Plugin | emu |

## Key TTPs

| Technique | Description |
|---|---|
| T1078 | Valid Accounts (MSP credentials) |
| T1059.001 | PowerShell |
| T1021.001 | RDP |
| T1003 | Credential Dumping |
| T1560 | Data Collection/Archive |
| T1071 | Application Layer Protocol C2 |
