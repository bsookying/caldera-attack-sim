---
type: Caldera Plugin
title: Atomic Plugin
description: The Atomic plugin imports Red Canary Atomic Red Team test cases as Caldera abilities, providing hundreds of additional ATT&CK-mapped ability definitions.
tags:
  - caldera
  - plugin
  - atomic
  - atomic-red-team
resource: /plugin/atomic/gui
sources:
  - id: art-github
    resource: https://github.com/redcanaryco/atomic-red-team
    title: Red Canary Atomic Red Team
    author: team:redcanary
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Atomic Plugin

The Atomic plugin translates [Red Canary Atomic Red Team](https://github.com/redcanaryco/atomic-red-team) test definitions into Caldera abilities.[^art-github] Atomic Red Team is an open library of small, focused tests mapped to ATT&CK techniques.

[^art-github]: Red Canary Atomic Red Team

## What It Adds

- Hundreds of additional ATT&CK-mapped abilities
- Many Windows-focused tests (T1059.001 PowerShell variations, T1003 credential dumping, etc.)
- Tests that validate specific detection logic
- Named after `ATH` prefix abilities (e.g., `ATHPowerShellCommandLineParameter`)

## Key Differences from Stockpile Abilities

- Atomic abilities are designed to be **precise, named tests** for specific ATT&CK sub-techniques.
- They are imported from YAML test definitions in the ART repository.
- They may have **more specific prerequisites** and cleanup steps.

## Usage

Atomic abilities appear alongside stockpile abilities in the ability library. Filter by `technique_id` to find Atomic tests for a specific ATT&CK technique:

```powershell
$abilities | Where-Object { $_.technique_id -eq "T1059.001" } | 
    Select-Object ability_id, name, plugin
```
