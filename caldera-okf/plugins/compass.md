---
type: Caldera Plugin
title: Compass Plugin
description: Compass provides an ATT&CK Navigator-style visualization to explore available abilities mapped to the ATT&CK matrix and export layer files.
tags:
  - caldera
  - plugin
  - compass
  - att&ck
  - visualization
resource: /plugin/compass/gui
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Compass Plugin

Compass provides an interactive ATT&CK matrix visualization within Caldera, similar to the [MITRE ATT&CK Navigator](https://mitre-attack.github.io/attack-navigator/). It highlights which ATT&CK techniques have abilities defined in the current Caldera instance.

## Capabilities

- Color-coded ATT&CK heatmap showing coverage across all tactics and techniques
- Filter by platform (Windows, Linux, macOS)
- Filter by adversary or plugin
- Export ATT&CK Navigator layer JSON files for reporting
- Identify gaps in detection/emulation coverage

## UI Access

Navigate to `/plugin/compass/gui` in the Caldera web interface.

## Use Cases

- Pre-engagement coverage analysis: "which techniques can we emulate?"
- Post-engagement reporting: export a layer showing what was executed
- Gap analysis: identify techniques in scope but lacking abilities
