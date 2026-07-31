---
type: API Reference
title: Payloads API
description: REST API endpoints for listing, uploading, and deleting Caldera payload files.
tags:
  - caldera
  - api
  - payloads
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Payloads API

246 payloads are currently loaded. See [concepts/payload](../concepts/payload.md).

```
GET    /api/v2/payloads                  # list all payload filenames
POST   /api/v2/payloads                  # upload a payload (multipart)
DELETE /api/v2/payloads/{filename}       # delete a payload
GET    /file/download                    # agent file download (internal)
```

## Upload a Payload

```bash
curl -b caldera-cookies.txt \
  -F "file=@./mimikatz.exe" \
  http://192.168.8.121:8888/api/v2/payloads
```

Max size: **100 MB**

## Agent File Download

Agents use this endpoint (not the API) to fetch payloads:

```
GET /file/download
Headers:
  file: sandcat.go-windows
  platform: windows
```

The server compiles/serves the appropriate binary on demand for Sandcat. For other payloads it returns the file directly.
