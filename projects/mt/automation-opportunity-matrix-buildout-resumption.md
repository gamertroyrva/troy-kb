# Restart: Automation Opportunity Matrix Buildout Resumption

*Project-specific context document. Attach alongside about-me.md, anti-ai-writing-style.md, troy-voice-examples.md, math-trade-magic.md, and excel-style-preferences.md when resuming this work.*

---

## What This Is

The **Automation Opportunity Matrix** ("the Matrix") is a step-level table built from the Math Trade Organizer Playbook. Its purpose is simple to state and genuinely useful to act on: it is the document Troy stares at to decide what to automate first, second, third — which steps in running a math trade get handed off to AI/automation, and in what order.

This is part of the larger **AI-Assisted Math Trade Organizer** project. The Matrix is the decision layer sitting between the Playbook (the raw content — what organizing a trade actually involves, phase by phase, task by task, step by step) and the actual building work (agents, scripts, connectors, automations that go live).

## Why It Exists

Running a math trade by hand is a lot of accumulated tribal knowledge and repeated motion — checking a GeekList, syncing to OLWLG, drafting the same kind of Facebook post every cycle, scanning a tracker for who hasn't joined yet. Some of that is pure mechanism: templated, repeatable, no judgment required. Some of it is irreducibly Troy — a relationship, a community read, a decision only he can make well. Most tasks are some mix of both.

The Matrix exists to pull that mixture apart, one step at a time, so that what's mechanical can be offloaded and what's not stays exactly where it belongs: with Troy. It's a forcing function for clarity, not just a record.

## Structure

Each row is a single step (not a task — tasks are too coarse a unit, since a single task often contains a mechanical sub-step and a judgment-driven sub-step bundled together).

| Column | Purpose |
|---|---|
| Label | Live formula concatenating Phase, Task, Step numbers (e.g., 1.1.1) |
| Phase | Phase number |
| Phase Name | Phase name |
| Task | Task number |
| Task Name | Task name |
| Step | Step number |
| Step Description | What this step is |
| Input | What's needed before this step can happen |
| Process | What's actually done |
| Output | What this step produces |
| Feasibility | 1_LOW / 2_MEDIUM / 3_HIGH — see definition below |
| Offload Value | 1_LOW / 2_MEDIUM / 3_HIGH — see definition below |

**Feasibility** — How readily this step could be handed to AI/automation for Troy, today. Three things can drag it down: the step requires human judgment or discretion; the tool or connector needed doesn't exist or isn't accessible (e.g., no Discord connector); or it requires capability Troy hasn't built yet. Optional terse color text after the score flags which of these is operative, when it's not obvious.

**Offload Value** — How much of the organizer's time/effort gets handed off when this step is automated, driven by how much time it currently costs and how often it recurs. Independent of the step's importance to the trade's success — a critical step can score Low here if it's irreducibly Troy's judgment. Optional terse color text when the score isn't self-explanatory.

Same-grade consecutive steps within a task may signal a natural "glump" — a single automation ticket covering 2-3 steps rather than one each. This is read off the table, not pre-engineered into it; Troy decides ticket boundaries.

## Formatting

All formatting follows `excel-style-preferences.md` — left-justified text columns, center-justified numeral columns, vertical centering throughout, automatic row height, bold black-on-mid-gray frozen header, gridlines plus one trailing blank row, live formulas over hardcoded values.

## Current Status

Phases 1-3 of the Math Trade Organizer Playbook are fully populated in the Matrix — 67 steps total across 17 tasks, delivered as a single-sheet Excel workbook (`automation-opportunity-matrix.xlsx`).

- **Phase 1 — Awareness & Recruiting**: 4 tasks, 10 steps
- **Phase 2 — Offer Up Games**: 5 tasks, 25 steps
- **Phase 3 — Wants Phase / Practice Runs**: 8 tasks, 32 steps

## What's Left

Three phases of the Math Trade Organizer Playbook have not yet been built out and are therefore not yet represented in the Matrix:

4. **Final Run Phase**
5. **Announce Results Phase**
6. **Swap Phase**

Each will need its own Playbook HTML pass (Description / Task Steps / Why Do This per task) before it can be poured into the Matrix using the same step-level structure and grading definitions already established.

## Open Threads / Things Worth Noticing

- A recurring drag on Feasibility across all three completed phases is the lack of a Claude-Discord connector — Discord shows up constantly (channel setup, monitoring, personal outreach, posting updates) and its absence as a connector caps Feasibility on a meaningful cluster of steps. Building or acquiring that connector could be a meta-lever, raising Feasibility across multiple rows at once rather than one at a time.
- Phase 3 (Wants Phase / Practice Runs) is the densest and most operationally repetitive phase — it's also where the clearest "Start Here" candidates live (Capture Practice Run Data, Update the Results Tracker), since those steps are both highly mechanical and recur every single practice run.
- Several tasks contain an internal split between a highly automatable surfacing/mechanical sub-step and a judgment-only sub-step (e.g., Monitoring GeekList Additions, Perform Anomaly Review, Monitoring & Nudging Submissions). Step-level grading was adopted specifically to make this split visible rather than averaged away.

---

*Created: June 2026.*
