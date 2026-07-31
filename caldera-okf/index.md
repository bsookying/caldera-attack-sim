---
okf_version: "0.2"
type: Bundle Index
title: Caldera Knowledge Bundle
description: Comprehensive OKF knowledge bundle for MITRE Caldera 5.3.0 — concepts, REST API, plugins, adversary profiles, and lab environment.
tags:
  - caldera
  - mitre
  - adversary-emulation
  - red-team
  - att&ck
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
---

# Caldera Knowledge Bundle

MITRE Caldera is an open-source adversary emulation platform built on the MITRE ATT&CK framework. This bundle covers Caldera 5.3.0 as deployed at `http://192.168.8.121:8888` in the GCP sandbox lab.

## Sections

* [overview](overview.md) - What Caldera is, its architecture, and core workflow
* [concepts](concepts/index.md) - Core building blocks: abilities, adversaries, agents, operations, facts, planners, obfuscators
* [api](api/index.md) - REST API v2 reference — authentication, endpoints, request/response patterns
* [plugins](plugins/index.md) - Enabled plugins and their capabilities
* [adversaries](adversaries/index.md) - Pre-built adversary profiles available in this instance
* [lab](lab/index.md) - Lab environment: target hosts, network segments, C2 contacts
* [operations](operations/index.md) - Three end-to-end adversary emulation operation designs with full ATT&CK kill chains and Caldera configs
* [video](video/index.md) - NotebookLM prompts for generating 3-minute customer-facing video narrations for each operation
