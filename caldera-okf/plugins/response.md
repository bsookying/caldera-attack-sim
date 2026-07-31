---
type: Caldera Plugin
title: Response Plugin
description: The Response plugin enables automated incident response actions triggered by operation events, supporting purple team and defensive validation exercises.
tags:
  - caldera
  - plugin
  - response
  - incident-response
  - blue-team
resource: /plugin/responder/gui
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Response Plugin

The Response plugin allows Caldera to automatically trigger defensive response actions when specific operation events occur. It supports purple team exercises where blue team responses are tested alongside red team TTPs.

## Capabilities

- Define response rules triggered by operation events (e.g., "when technique X runs, trigger response Y")
- Automated defensive actions: isolate host, kill process, block IP, quarantine file
- Blue team agent support: deploy defensive agents that monitor and respond
- Alert generation integrated with the Caldera UI

## Use Cases

- Purple team exercises: test detection and response in a single framework
- Validate SOAR playbook triggers
- Measure response time to specific ATT&CK techniques
- Test containment procedures without production risk

## UI Access

`/plugin/responder/gui`
