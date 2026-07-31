# Adversary Emulation Operations

Three end-to-end operation designs for this lab, each telling a distinct threat story across the full ATT&CK kill chain.

## Operation Design Docs (ATT&CK kill chain, Caldera config, detection opportunities)

* [op1_silent_fog](op1_silent_fog.md) - Nation-state espionage (APT29-style) · 12 phases · windows-client-11 → windows-dc
* [op2_crimson_tide](op2_crimson_tide.md) - Ransomware campaign (Wizard Spider-style) · 10 phases · windows-the-boss → domain-wide encryption
* [op3_dark_pivot](op3_dark_pivot.md) - Supply chain via developer station · 12 phases · windows-dev-station → DC + IP exfil

## Operator Playbooks (step-by-step execution, demo narration, cleanup)

* [playbook_op1_silent_fog](playbook_op1_silent_fog.md) - APT29 · 25–40 min · caldera-1 · entry: windows-client-11
* [playbook_op2_crimson_tide](playbook_op2_crimson_tide.md) - Wizard Spider ransomware · 10–15 min · caldera-1 · entry: windows-the-boss
* [playbook_op3_dark_pivot](playbook_op3_dark_pivot.md) - APT10 supply chain · 20–35 min · caldera-2 · entry: windows-dev-station
