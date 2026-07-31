---
type: Adversary Profile
title: Turla - Snake
description: Turla Snake is a sophisticated kernel-level implant and C2 framework used by the Turla APT group; the emulation profile covers 53 abilities.
tags:
  - caldera
  - adversary
  - turla
  - snake
  - uroburos
  - russia
  - emu
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Turla - Snake

Snake (also Uroburos) is Turla's most sophisticated implant — a kernel-level rootkit with encrypted P2P C2 infrastructure. It represents Turla's highest-tier capability, used in top-priority espionage targets.

## Caldera Profile

| Field | Value |
|---|---|
| Adversary ID | `2c03e8df-284d-4bda-900d-1c9523fba7eb` |
| Abilities | 53 |
| Recommended Planner | atomic (`aaa7c857-37a0-4c4a-85f7-4e9f7f30e31a`) |
| Fact Source ID | `19d4b2b9-a2d0-530f-a893-2319807df06b` (Turla - Snake Emu — 21 seed facts) |
| Plugin | emu |

## Key TTPs

| Technique | Description |
|---|---|
| T1014 | Rootkit |
| T1090.001 | Internal Proxy |
| T1573 | Encrypted Channel |
| T1105 | Tool Transfer |
| T1070 | Indicator Removal |
| T1056 | Input Capture |
