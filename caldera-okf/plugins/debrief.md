---
type: Caldera Plugin
title: Debrief Plugin
description: Debrief provides post-operation reporting, timeline visualization, and PDF export for completed Caldera operations.
tags:
  - caldera
  - plugin
  - debrief
  - reporting
resource: /plugin/debrief/gui
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Debrief Plugin

Debrief is Caldera's post-operation reporting plugin. After an operation completes, Debrief generates a structured summary showing what happened, in what order, and with what results.

## Capabilities

- **Timeline view**: chronological list of all links executed, with timestamps and outcomes
- **ATT&CK coverage**: heatmap of techniques actually executed during the operation
- **Fact summary**: all facts collected throughout the operation
- **PDF export**: generate a printable PDF report for stakeholders
- **Multi-operation comparison**: compare coverage across multiple runs

## UI Access

Navigate to `/plugin/debrief/gui` and select a completed operation.

## Use Cases

- Operator after-action review
- Client deliverable for red team engagements
- Gap analysis: techniques attempted vs. successful
- Evidence capture for compliance exercises
