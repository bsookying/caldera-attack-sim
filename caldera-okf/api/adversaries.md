---
type: API Reference
title: Adversaries API
description: REST API endpoints for listing, creating, and managing Caldera adversary profiles.
tags:
  - caldera
  - api
  - adversaries
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Adversaries API

See [concepts/adversary](../concepts/adversary.md) for the full schema and profile list.

## Endpoints

```
GET    /api/v2/adversaries              # list all (39 available)
GET    /api/v2/adversaries/{id}         # get one
POST   /api/v2/adversaries              # create
PATCH  /api/v2/adversaries/{id}         # update
DELETE /api/v2/adversaries/{id}         # delete
```

## Create a Custom Adversary

```json
POST /api/v2/adversaries
{
  "name": "Workstation Recon",
  "description": "Discovery and credential harvest against workstations",
  "atomic_ordering": [
    "567eaaba-94cc-4a27-83f8-768e5638f4e1",
    "ba0b398d-91b8-490a-bed2-f959afa8e1aa",
    "2d78dfbfea7fa4ec7349993b83048079"
  ]
}
```

## Key Adversary IDs

| ID | Name | Abilities |
|---|---|---|
| `4975696e-1d41-11eb-adc1-0242ac120002` | APT29 | 79 |
| `186b4d47-f4c0-48cb-9688-887070db45f1` | Carbanak | 24 |
| `123700e5-44c8-4894-a409-6484992c8846` | FIN6 | 27 |
| `c30763a7-172c-431e-a877-b18aecf9292a` | FIN7 | 10 |
| `db88ae87-8884-42f1-aa06-d7ef9b45721d` | OilRig | 44 |
| `c397ca4d-3c68-414e-9f78-2a96a11edbef` | Sandworm Team | 40 |
| `eddf38a4-e483-48ec-8874-4983c7a3652b` | Turla - Carbon | 45 |
| `2c03e8df-284d-4bda-900d-1c9523fba7eb` | Turla - Snake | 53 |
| `23ee980e-f0c5-4e88-a02c-56aa862b2c76` | Wizard Spider | 38 |
| `b5192070-dff1-4134-bf66-c6b87c7498af` | menuPass | 15 |
| `0f4c3c67-845e-49a0-927e-90ed33c044e0` | Discovery | 12 |
| `ef4d997c-a0d1-4067-9efa-87c58682db71` | Defense Evasion | 36 |
| `785baa02-df5d-450a-ab3a-1a863f22b4b0` | Everything Bagel | 2,360 |
