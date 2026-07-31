---
type: Caldera Concept
title: Planner
description: A planner is the decision engine that determines which abilities to send to which agents each operation phase.
tags:
  - caldera
  - planner
  - scheduling
generated:
  by: claude-sonnet-4-6
  at: '2026-07-30T16:52:00Z'
status: stable
---

# Planner

A **planner** controls the execution logic of a Caldera [operation](operation.md) — deciding which [abilities](ability.md) to send to which [agents](agent.md) each phase, and in what order. This instance has **9 planners**.

## Available Planners

### atomic (`aaa7c857-37a0-4c4a-85f7-4e9f7f30e31a`) ⭐ Default
Iterates through agents one at a time, sending each the next ability in the adversary's ordered list. Waits for each agent to complete before moving to the next. Respects the `atomic_ordering` of the adversary exactly.

**Best for:** Precise, sequential emulation where order matters.

---

### batch (`788107d5-dc1e-4204-9269-38df0186d3e7`)
Sends all applicable abilities to all agents simultaneously in each phase, then waits for all to complete before advancing. In phaseless mode, runs everything in one shot.

**Best for:** Coverage testing — run all abilities as fast as possible.

---

### buckets (`f36c34f5-9439-4417-9640-fe83f4b7b12d`)
Variant of batch that groups abilities by ATT&CK tactic (bucket). Processes buckets in ATT&CK matrix order: reconnaissance → initial access → execution → persistence → privilege escalation → defense evasion → credential access → discovery → lateral movement → collection → exfiltration → command-and-control → impact.

**Best for:** Tactic-ordered operations that follow the kill chain.

---

### guided (`5bdec528-07ce-44cc-8997-09fe41aa2007`)
Maps abilities to goal states and executes them in the order most likely to achieve the operation's [objective](objective.md) in the fewest steps.

**Best for:** Goal-driven operations with defined success conditions.

---

### bayes (`48e1a882-1606-4910-8f2d-2352eb80cba2`)
Uses Bayesian statistics from past operational data to rank abilities by probability of success. Drops abilities below a configurable success threshold. Prioritizes high-confidence abilities first.

**Best for:** Adaptive operations that learn from historical execution data.

---

### look ahead (`254c7035-de7d-4d76-a888-2c09ba594eca`)
Scores each candidate ability based on anticipated future reward using a configurable depth (look-ahead window) and discount factor. Selects the action that maximizes cumulative expected value.

**Best for:** Reward-optimized operations where long-term outcomes matter.

---

### OilRig Planner (`0a7d8b12-c0fa-4aec-a890-368f3735f7a9`)
Specialized planner for the OilRig adversary emulation profile. Requires agents in groups: `kali` (attacker Linux) and `gosta` (target).

**Best for:** OilRig EMU profile only.

---

### Sandworm Planner (`6f46e3a4-f20e-49ed-8088-32a3094aad77`)
Specialized planner for the Sandworm adversary emulation profile. Requires agent groups: `kali` and `arrakis`. Spawns agents in `caladan` and `gammu` groups during the operation.

**Best for:** Sandworm EMU profile only.

---

### Wizard Spider Planner (`43f4e42d-5c93-43a7-89f4-227071014893`)
Specialized planner for the Wizard Spider adversary emulation profile. Requires agent groups: `kali`, `dorothy`, `toto`, and `wizard`.

**Best for:** Wizard Spider EMU profile only.

---

## Choosing a Planner

| Goal | Recommended Planner |
|---|---|
| Precise threat actor emulation | `atomic` |
| Maximum ability coverage | `batch` |
| Kill-chain ordered testing | `buckets` |
| Goal-driven scenario | `guided` |
| Adaptive / self-optimizing | `bayes` |
| EMU named-actor profiles | Actor-specific planner |

## API

```
GET /api/v2/planners            # list all planners
GET /api/v2/planners/{id}       # get one planner
```
