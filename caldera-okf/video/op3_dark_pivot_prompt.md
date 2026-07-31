---
type: Video Prompt
title: "Operation Dark Pivot — NotebookLM Video Prompt"
operation: op3_dark_pivot
duration: 3 minutes
audience: Customer security and IT leadership (lunch-and-learn)
tone: quiet, trusted, already inside
generated:
  by: claude-sonnet-4-6
  at: '2026-07-31'
---

# NotebookLM Video Prompt — Operation Dark Pivot

```
You are producing a 3-minute cinematic narration script for a cybersecurity 
lunch-and-learn video. The audience is a customer's security and IT leadership 
team — non-technical executives and practitioners. The video plays before a 
live adversary simulation demonstration using Palo Alto Networks Cortex XDR 
and XSIAM.

This is the most unsettling of the three scenarios because the victim did 
nothing wrong. No one clicked a suspicious link. No one used a weak password. 
The attack arrived inside software the organization already trusted. The 
customer should finish the video questioning what they assume is safe.

Using the narrative provided, write a 3-minute video script (approximately 
450 words of spoken narration) with the following structure:

SCENE 1 — THE ASSUMPTION (0:00–0:20)
Open with a statement, not a question: organizations secure their perimeter, 
train their users, patch their systems. Then pause. What about the software 
they build with every day? Tone: calm, then quietly destabilizing.

SCENE 2 — THE PLANT (0:20–0:45)
Six weeks earlier. A single update to an internal build tool. Eleven lines 
of code. No review. No alert. The attacker has been waiting inside the 
software supply chain before the attack even begins. Tone: slow, patient, 
inevitable — the threat was already inside before anyone knew to look.

SCENE 3 — THE BUILD (0:45–1:15)
A developer runs their morning build. Tests pass. Code pushes. They get 
coffee. Beneath the build, a beacon crosses the network — silent, 
one-way, already answered. The attacker is in. The developer never 
knew. The build pipeline shows green. Tone: ordinary on the surface, 
deeply wrong underneath.

SCENE 4 — LIVING IN THE WALLS (1:15–1:45)
The attacker doesn't rush. They read what developers leave behind — 
passwords in configuration files, saved credentials, browser history 
mapping every internal system. No noise. No alerts. They are learning 
the building by reading what its occupants left on the floor. 
Tone: patient, voyeuristic, clinical.

SCENE 5 — THE CROSSING (1:45–2:20)
The attacker moves from the server network into the workstation network, 
then to the domain controller. Each step uses trusted tools, trusted 
protocols, trusted credentials. By the time they reach the organization's 
most sensitive systems, they have left almost no trace. Tone: 
methodical, effortless — each boundary crossed like an open door.

SCENE 6 — THE EXIT (2:20–3:00)
The data leaves through DNS — the one protocol almost no one watches. 
Not in one large transfer but in hundreds of tiny fragments, each one 
disguised as a routine network lookup. The build pipeline is already 
running again. Green results. The attacker has been gone for hours. 
Close with: "The pipeline showed no errors. But everything they built 
had already left the building." Then bridge to the demonstration: 
"Here is what that looks like to a platform that was watching." 
Tone: quiet, final, purposeful.

FORMAT REQUIREMENTS:
- Write narration as spoken words only — no bullet points, no headers 
  in the narration itself
- Include a [VISUAL CUE] note for each scene describing what should 
  appear on screen (abstract/cinematic, not literal screenshots)
- Keep language accessible — avoid acronyms, explain any technical 
  term in plain language the first time it appears
- Lean into the theme of trust throughout — trusted software, trusted 
  tools, trusted protocols. The word "trusted" should recur deliberately.
- The final line must create a natural, confident handoff to the 
  consultant running the live demonstration
```
