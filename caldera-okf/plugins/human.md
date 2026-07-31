---
type: Plugin Reference
title: Human Plugin
description: Simulated user activity plugin — deploys fake browser/OS actions on targets to disguise red-team traffic as legitimate user behavior.
tags:
  - plugin
  - human-emulation
  - stealth
generated:
  by: claude-sonnet-4-6
  at: '2026-07-31T00:00:00Z'
status: stable
---

# Human Plugin

The `human` plugin gives Caldera human-emulation capabilities. It deploys simulated user sessions on target machines — browsing with Chrome, interacting with native OS applications — to disguise red-team activity as normal user behavior.

**Source:** https://github.com/mitre/human (commit 395e93cebe7abcc2c902159ca8afd92d53eb5f1f)

## Status

Enabled on both Caldera instances as of 2026-07-31.

| Instance | Enabled |
|---|---|
| caldera-1 (192.168.8.121) | Yes |
| caldera-2 (192.168.3.21) | Yes |

## Target Requirements

- Linux, macOS, or Windows (PowerShell)
- Python 3 + `virtualenv`
- Google Chrome

## Plugin Requirements (server-side)

Already installed in both containers:

```
lorem==0.1.1
selenium>=4.14.0
webdriver-manager==3.5.2
pyautogui==0.9.53
beautifulsoup4
```
