---
type: Caldera Plugin
title: EMU Plugin
description: The EMU plugin provides CTID adversary emulation plans — detailed, intelligence-driven profiles for named threat actors including APT29, OilRig, Sandworm, and more.
tags:
  - caldera
  - plugin
  - emu
  - adversary-emulation
  - ctid
resource: /plugin/emu/gui
sources:
  - id: ctid-plans
    resource: https://github.com/center-for-threat-informed-defense/adversary_emulation_library
    title: CTID Adversary Emulation Library
    author: team:ctid
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# EMU Plugin

The EMU (Emulation) plugin provides adversary profiles developed by the [Center for Threat-Informed Defense (CTID)](https://ctid.mitre-engenuity.org/) as part of the [Adversary Emulation Library](https://github.com/center-for-threat-informed-defense/adversary_emulation_library). These are intelligence-backed, multi-stage emulation plans with custom planners and fact sources.[^ctid-plans]

[^ctid-plans]: CTID Adversary Emulation Library

## Available EMU Adversaries

| Profile | Abilities | Planner | Fact Source |
|---|---|---|---|
| APT29 | 79 | atomic | APT29 (Emu) |
| OilRig | 44 | OilRig Planner | OilRig (Emu) |
| Sandworm Team (G0034) | 40 | Sandworm Planner | Sandworm (Emu) |
| Turla - Carbon | 45 | atomic | Turla - Carbon (Emu) |
| Turla - Snake | 53 | atomic | Turla - Snake (Emu) |
| Wizard Spider | 38 | Wizard Spider Planner | Wizard Spider (Emu) |
| Carbanak | 24 | atomic | Carbanak (Emu) |
| FIN6 | 27 | atomic | FIN6 (Emu) |
| FIN7 | 10 | atomic | FIN7 (Emu) |
| menuPass | 15 | atomic | menuPass (Emu) |

## Key Differences from Stockpile Adversaries

- EMU profiles use **real-world TTPs** sourced from threat intelligence reports.
- Many EMU profiles require **specific agent groups** (e.g., `kali`, `gosta`, `arrakis`) and dedicated planners that coordinate multi-host scenarios.
- EMU profiles come with **pre-configured fact sources** containing the specific variables each phase needs.
- Some EMU abilities require an attacker-controlled Linux machine (`kali` group) to simulate external adversary actions.

## Multi-Host EMU Scenarios

Some EMU profiles span multiple hosts and require multiple agents in different groups:

| Profile | Required Agent Groups |
|---|---|
| OilRig | `kali` (attacker Linux), `gosta` (target) |
| Sandworm | `kali`, `arrakis`; spawns `caladan`, `gammu` |
| Wizard Spider | `kali`, `dorothy`, `toto`, `wizard` |

## Running an EMU Profile

```json
POST /api/v2/operations
{
  "name": "APT29 Emulation Run 1",
  "adversary": {"adversary_id": "4975696e-1d41-11eb-adc1-0242ac120002"},
  "group": "red",
  "planner": {"id": "aaa7c857-37a0-4c4a-85f7-4e9f7f30e31a"},
  "source": {"id": "152ca62e-32b6-5c2a-84e8-89fedd44d621"},
  "obfuscator": "base64",
  "autonomous": 1,
  "jitter": "5/15"
}
```
