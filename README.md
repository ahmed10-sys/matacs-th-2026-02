# Matacs Technical Assessment
**Reference:** MATACS-TH-2026-02
**Candidate:** Syed Ahmed Haroon
**Time Spent:** ~65 minutes

## Required Files
* [diagram/flow_diagram.png](diagram/flow_diagram.png) - Process flow swimlane diagram.
* [WRITEUP.md](WRITEUP.md) - Answers to the 5 design questions.
* [PROMPTS.md](PROMPTS.md) - Dynamic AI extraction prompt template.
* [ranking.json](ranking.json) - Deterministic ranking output for proposals A-E.

## AI Usage Statement
I used Claude as a thought partner to refine my architecture and debate edge cases. First, I disagreed with the AI when it initially suggested hard-coding `PROMPTS.md` strictly for the Commercial Solar scenario, correcting it to use a dynamic template that adapts to any category. Second, I kept a strict system boundary where the AI is only used to extract raw data and quotes, while the deterministic code layer handles all math, logic, and final rankings.