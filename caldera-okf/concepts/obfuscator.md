---
type: Caldera Concept
title: Obfuscator
description: An obfuscator encodes or transforms ability commands before they are sent to agents, helping evade detection while still executing correctly.
tags:
  - caldera
  - obfuscator
  - evasion
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Obfuscator

An **obfuscator** transforms ability commands before they are delivered to [agents](agent.md), providing a layer of evasion against command-line detection. The agent decodes and executes the original command. This instance has **6 obfuscators**.

## Available Obfuscators

| Name | Description |
|---|---|
| `plain-text` | No obfuscation — commands sent as-is |
| `base64` | Commands encoded in standard Base64 |
| `base64noPadding` | Base64 with `=` padding stripped |
| `base64jumble` | Base64 with additional random characters to evade Base64 detection signatures |
| `caesar cipher` | Caesar cipher with a randomly selected shift value |
| `steganography` | Commands hidden inside image files |

## Usage Notes

- `plain-text` is best for debugging or environments where detection is not a concern.
- `base64jumble` may cause **duplicate links** to run (noted in the Caldera UI as a disclaimer).
- `steganography` has the highest evasion potential but requires the agent to support image decoding.
- Obfuscators are set per-operation and apply uniformly to all commands in that operation.

## Setting an Obfuscator

Specify in the operation creation payload:

```json
POST /api/v2/operations
{
  "obfuscator": "base64",
  ...
}
```

## API

```
GET /api/v2/obfuscators          # list all obfuscators
GET /api/v2/obfuscators/{name}   # get one
```
