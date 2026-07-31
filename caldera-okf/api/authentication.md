---
type: API Reference
title: Caldera API Authentication
description: Caldera 5.x uses session-cookie authentication via POST /enter with multipart form data; the API_SESSION cookie must be included in all subsequent requests.
tags:
  - caldera
  - api
  - authentication
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Authentication

Caldera 5.x (Magma UI) uses **session cookie authentication**. The login endpoint is `/enter` (not `/api/v2/login`). All subsequent API calls must include the `API_SESSION` cookie.

## Login Flow

```
POST /enter
Content-Type: multipart/form-data

username=admin
password=admin
```

On success: HTTP 200 + `Set-Cookie: API_SESSION=<encrypted-token>`

On failure: HTTP 401

## Credentials (this instance)

| User | Password | Access Level |
|---|---|---|
| `admin` | `admin` | RED (full operator) |
| `red` | `admin` | RED |
| `blue` | `admin` | BLUE (monitoring only) |

## PowerShell Example

```powershell
$session = New-Object Microsoft.PowerShell.Commands.WebRequestSession
$boundary = "----Boundary" + [Guid]::NewGuid().ToString("N")
$body = "--$boundary`r`nContent-Disposition: form-data; name=`"username`"`r`n`r`nadmin`r`n" +
        "--$boundary`r`nContent-Disposition: form-data; name=`"password`"`r`n`r`nadmin`r`n" +
        "--$boundary--"

Invoke-WebRequest -Uri "http://192.168.8.121:8888/enter" -Method Post `
    -Body $body -ContentType "multipart/form-data; boundary=$boundary" `
    -WebSession $session -UseBasicParsing | Out-Null

# All subsequent calls use -WebSession $session
$health = Invoke-WebRequest -Uri "http://192.168.8.121:8888/api/v2/health" `
    -WebSession $session -UseBasicParsing
```

## Python Example

```python
import requests

s = requests.Session()
s.post("http://192.168.8.121:8888/enter",
       data={"username": "admin", "password": "admin"})

# Now use s for all API calls
health = s.get("http://192.168.8.121:8888/api/v2/health").json()
```

## curl Example

```bash
# Login and save cookie
curl -c caldera-cookies.txt \
  -F "username=admin" \
  -F "password=admin" \
  http://192.168.8.121:8888/enter

# Use saved cookie for API calls
curl -b caldera-cookies.txt \
  http://192.168.8.121:8888/api/v2/health
```

## Auth Status Check

```
HEAD /api/v2/config/main
```

- HTTP 200 → authenticated
- HTTP 401 → not authenticated / session expired

## Session Details

- Cookie name: `API_SESSION`
- Session expiry: **7 days** (configured in `session_expiration_days`)
- The cookie value is an encrypted Fernet token; treat it as a secret

## Important Notes

- The `/api/v2/login` endpoint exists but returns 401 for password auth; use `/enter` instead.
- Do **not** use form-urlencoded for `/enter` — it returns 405. Use `multipart/form-data`.
- PowerShell: all login and API calls must be in a **single session** — `$session` does not persist across separate PowerShell invocations.
