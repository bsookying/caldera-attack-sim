---
type: Video Prompt
title: "Operation Silent Fog — NotebookLM Video Prompt"
operation: op1_silent_fog
duration: 3 minutes
audience: Customer security and IT leadership (lunch-and-learn)
tone: slow, patient, invisible
generated:
  by: claude-sonnet-4-6
  at: '2026-07-31'
---

# NotebookLM Video Prompt — Operation Silent Fog

```
You are producing a 3-minute cinematic narration script for a cybersecurity 
lunch-and-learn video. The audience is a customer's security and IT leadership 
team — non-technical executives and practitioners. The video plays before a 
live adversary simulation demonstration using Palo Alto Networks Cortex XDR 
and XSIAM.

Your goal is to make the attack feel real, personal, and inevitable — not 
technical. The customer should finish the video thinking: "this could happen 
to us."

Using the narrative provided, write a 3-minute video script (approximately 
450 words of spoken narration) with the following structure:

SCENE 1 — THE HOOK (0:00–0:20)
Open with a single, quiet moment. A person at a desk. An ordinary Tuesday. 
Set the scene without revealing the threat yet. Tone: calm, almost mundane.

SCENE 2 — THE CLICK (0:20–0:45)
The spearphishing email arrives. The analyst opens it. Nothing seems wrong. 
Narration should convey the invisibility of the moment — the attack has 
already begun, and no one knows. Tone: quiet dread.

SCENE 3 — THE WAIT (0:45–1:15)
The attacker goes still. Eighteen hours pass. The implant sits, beaconing 
quietly. The attacker is patient. The organization goes about its day 
completely unaware. Tone: slow, deliberate, unsettling.

SCENE 4 — THE SPREAD (1:15–2:00)
The attacker begins moving — credentials harvested, lateral movement to a 
senior employee's machine, then to the domain controller. Each step described 
as inevitable, effortless. Tone: momentum building, almost clinical.

SCENE 5 — THE TAKE (2:00–2:40)
NTDS.dit. File shares. Sensitive documents. Everything staged, compressed, 
and exfiltrated slowly over eleven days. Event logs wiped. Tracks covered. 
The organization still does not know. Tone: cold, final.

SCENE 6 — THE REVEAL (2:40–3:00)
Pull back. The attack is over. The data is gone. The backdoor account still 
exists. End with a single question that bridges to the live demonstration: 
"What if someone had been watching?" Tone: measured, purposeful.

FORMAT REQUIREMENTS:
- Write narration as spoken words only — no bullet points, no headers in 
  the narration itself
- Include a [VISUAL CUE] note for each scene describing what should appear 
  on screen (abstract/cinematic, not literal screenshots)
- Keep language accessible — avoid acronyms, explain any technical term in 
  plain language the first time it appears
- The final line should naturally hand off to the live demonstration
```
