---
type: Adversary Profile
title: Turla - Carbon
description: Turla Carbon is a Russian FSB-attributed framework used by the Turla APT group for long-term espionage; emulation covers 45 abilities.
tags:
  - caldera
  - adversary
  - turla
  - carbon
  - russia
  - fsb
  - emu
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Turla - Carbon

Turla (Venomous Bear, Snake, Uroburos) is a Russian FSB-attributed APT group conducting long-term espionage campaigns against governments, embassies, and defense contractors. The Carbon framework is their primary second-stage backdoor.

## Caldera Profile

| Field | Value |
|---|---|
| Adversary ID | `eddf38a4-e483-48ec-8874-4983c7a3652b` |
| Abilities | 45 |
| Recommended Planner | atomic (`aaa7c857-37a0-4c4a-85f7-4e9f7f30e31a`) |
| Fact Source ID | `67282c29-93bc-543c-a53f-3b4ecac2ec42` (Turla - Carbon Emu — 21 seed facts) |
| Plugin | emu |

## Key TTPs

| Technique | Description |
|---|---|
| T1055 | Process Injection |
| T1071.001 | Web Protocol C2 |
| T1105 | Ingress Tool Transfer |
| T1021.002 | SMB Lateral Movement |
| T1003 | Credential Dumping |
| T1560 | Archive Collected Data |
