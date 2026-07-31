---
type: Caldera Plugin
title: Stockpile Plugin
description: Stockpile is the core Caldera ability and adversary library providing generic ATT&CK-mapped abilities and scenario-based adversary profiles.
tags:
  - caldera
  - plugin
  - stockpile
  - abilities
resource: /plugin/stockpile/gui
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Stockpile Plugin

Stockpile is the built-in library of Caldera abilities and adversary profiles. It provides the largest set of general-purpose ATT&CK-mapped abilities and is the source of scenario adversaries like "Discovery", "Defense Evasion", and "Super Spy".

## What It Provides

- The bulk of the 2,360 abilities in this instance
- Scenario adversaries (Discovery, Defense Evasion, Lateral Movement, Ransack, etc.)
- Pre-built fact sources (basic, Exfil Operation)
- Objectives
- Payloads and helper scripts

## Notable Stockpile Adversaries

| Name | Abilities | Purpose |
|---|---|---|
| Discovery | 12 | Pure enumeration/discovery abilities |
| Defense Evasion | 36 | AV bypass, log clearing, obfuscation |
| Ransack | 18 | File collection and staging |
| Super Spy | 15 | Combined espionage scenario |
| Worm | 13 | Self-propagation scenario |
| Nosy Neighbor | 7 | Network reconnaissance |
| Collection | 4 | Data collection scenario |
| Enumerator | 5 | System enumeration |
| Advanced Thief | 3 | Exfiltration via HTTP |
| Everything Bagel | 2,360 | All abilities (kitchen sink) |

## Ability Categories

Stockpile abilities span all ATT&CK tactics and support Windows, Linux, and macOS. Shell executors include PowerShell (`psh`), cmd, bash (`sh`), and Python.
