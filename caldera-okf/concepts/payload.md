---
type: Caldera Concept
title: Payload
description: Payloads are binary or script files that Caldera serves to agents during operations for execution on target hosts.
tags:
  - caldera
  - payload
  - binary
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Payload

A **payload** is a file (binary, script, or archive) that Caldera serves to [agents](agent.md) during an [operation](operation.md). When an [ability](ability.md) executor declares a payload, the agent downloads it from the Caldera server before executing the ability command. This instance has **246 payloads**.

## How Payloads Work

1. An ability's executor lists required payloads by filename.
2. When a link is sent to an agent, the agent fetches each payload from:
   ```
   GET /file/download
   Headers: file:<filename>, platform:<platform>
   ```
3. The agent writes the payload to its working directory.
4. The ability command references the payload by filename.

## Payload Types

| Type | Examples |
|---|---|
| Windows executables | `mimikatz.exe`, `PsExec.exe`, `procdump.exe` |
| PowerShell scripts | `*.ps1` |
| Bash/shell scripts | `*.sh` |
| Python scripts | `*.py` |
| Compiled tools | Sandcat binaries for lateral movement |
| Archives | `*.zip`, `*.tar.gz` |

## Uploading a Custom Payload

```bash
curl -X POST http://192.168.8.121:8888/api/v2/payloads \
  -H "Cookie: API_SESSION=<token>" \
  -F "file=@/path/to/tool.exe"
```

Max upload size: **100 MB** (configured in server).

## API

```
GET    /api/v2/payloads              # list all payload filenames
POST   /api/v2/payloads              # upload a payload
DELETE /api/v2/payloads/{filename}   # remove a payload
GET    /file/download                # agent file download endpoint
```
