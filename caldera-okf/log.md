# Caldera Bundle Update Log

## 2026-07-31
* **Caldera SSH**: Created three fact sources via YAML files written directly into the container overlay filesystem (Caldera runs in a containerd overlay, API POST with inline facts returns 500 — known limitation). Sources reachable via API after restart.
  - Silent Fog - Lab Targets: a1b2c3d4-0001-0001-0001-000000000001
  - Crimson Tide - Lab Targets: a1b2c3d4-0002-0002-0002-000000000002
  - Dark Pivot - Dev Segment: a1b2c3d4-0003-0003-0003-000000000003
* **All operations**: Source IDs updated in operation launch JSON configs.
* **Caldera API**: Created custom adversary "Operation Silent Fog - APT29" (b63f9720-9203-40fc-9d40-1f8fd58167cd) — 22-ability trimmed profile replacing the bloated built-in APT29 (76 abilities).
* **Caldera API**: Updated "Operation Dark Pivot" adversary — replaced email collection ability (b1dcc53a) with Chrome (9bc50b2e) + Edge (27a2c04f) browser history abilities at position 25. Both included for test comparison.
* **op1_silent_fog.md**: Updated adversary ID to custom Silent Fog profile (b63f9720).
* **op2_crimson_tide.md**: Filled placeholder adversary ID (a17c9651-4ebb-4883-b67b-d5854eb512c1).
* **op3_dark_pivot.md**: Filled placeholder adversary ID (003e3455-d435-4353-be68-b5989a2fb371), updated ability manifest to reflect Chrome + Edge browser history entries.
* **video/**: New section added. NotebookLM video prompts created for all three operations — 3-minute cinematic narration scripts for customer lunch-and-learn sessions, designed to play before the live Cortex XDR / XSIAM demonstration.
* **All operations**: Added full prose narratives (Narrative section) to all three operations — Silent Fog, Crimson Tide, Dark Pivot.
* **op1_silent_fog**: Added Pre-Operation Setup section — PowerShell commands to disable Defender AV, Defender Firewall, and SmartScreen on all target hosts before agent deployment. Marked as tentative pending test run (may be removed if agent runs cleanly without it).
* **op2_crimson_tide**: Reviewed — no changes. Pre-op Defender/firewall disable section deferred pending test run (same as Silent Fog).
* **op3_dark_pivot**: Step 25 changed from Outlook email collection (T1114.001) to browser history collection (T1217) — lab has no email server. Ability ID updated throughout.
* **op3_dark_pivot**: C2 switched from caldera-1 (192.168.8.121, Server segment) to caldera-2 (192.168.3.21, Workstation segment) — ensures dev-station beacon traffic traverses the firewall rather than staying within the Server VLAN.
* **lab/environment.md**: Updated to document dual Caldera C2 assignment rationale across all three operations.

## 2026-07-30
* **Creation**: Initial bundle created from live Caldera 5.3.0 instance at 192.168.8.121.
* **Creation**: Indexed 2,360 abilities across 20 tactics, 39 adversary profiles, 9 planners, 6 obfuscators, 13 fact sources, 14 plugins.
* **Creation**: Documented all REST API v2 endpoints with authentication flow.
* **Creation**: Added lab environment context with host inventory and C2 contact channels.
* **Creation**: Designed 3 adversary emulation operations with full kill chains and Caldera configs — Operation Silent Fog (APT29), Operation Crimson Tide (ransomware), Operation Dark Pivot (supply chain).
