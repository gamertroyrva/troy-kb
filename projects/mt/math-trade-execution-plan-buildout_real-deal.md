# Handoff: AI-Assisted Math Trade Execution Plan (IPO Table) — v2

*Restart document for building the IPO table in a fresh Claude Chat session.*
*Supersedes the prior handoff (math-trade-execution-plan-handoff.md) — see "What Changed" below.*

---

## What I'm Building

A comprehensive execution plan for running an RVA No-Ship Math Trade, structured as an IPO table (Input / Process / Output) with explicit **Feasibility** and **Leverage** grades on every task row. The purpose of staring at this table is to identify which tasks are candidates for agentic AI ownership — and to begin prioritizing which ones to tackle first.

This is Backlog Item 7: Create the AI-Assisted Math Trade System. The June 27 GeekList launch for the RVA no-ship trade (swap day July 25) is the near-term forcing function: the table should be done and prioritized within 1-2 days so building can begin before June 27, and continue once the trade is live ("continue building stuff when the plane is flying").

---

## What Changed From the Prior Handoff

1. **Grading system replaced.** The old single "AI Opportunity" column (with working labels like Start Here / Easy Win / Human-in-the-Loop / No Way Jose) is replaced by **two explicit columns — Feasibility and Leverage** — each scored 1_Low / 2_Medium / 3_High. The 2x2 synthesis (Start Here, Easy Win, Human-in-the-Loop, No Way Jose) still applies conceptually but emerges visually from the two scores rather than being pre-collapsed into a label.

2. **Source material is now real, not hypothetical.** Phases 1-3 have been fully elaborated and rendered as HTML since the prior handoff. The old bone structure (a/b/c/d hypotheses for all six phases) is **stale for Phases 1-3** and should be discarded in favor of the actual content in the three HTML files (see below). Phases 4-6 bone structure from the prior handoff remains as the only available reference for those phases, but **Phases 4-6 are out of scope for this table** — only Phases 1-3 (17 tasks total) are in scope right now.

---

## The Deliverable Structure

| Phase | Task | Input | Process | Output | Feasibility | Leverage |
|-------|------|-------|---------|--------|--------------|-----------|

- **Phase** — phase name (e.g., "Wants Phase / Practice Runs")
- **Task** — task name as titled in the Phase HTML (e.g., "Capture Practice Run Data")
- **Input** — what's needed before this can happen (derive from the task's Description/Steps)
- **Process** — what is actually done (derive from the task's Steps)
- **Output** — what it produces (derive from the task's Description/Why)
- **Feasibility** — 1_Low / 2_Medium / 3_High. How readily this Process could be handed to AI/automation given tools that plausibly exist or could be wired up without heroics. Main drag: human judgment, discretion, community read, relationship nuance (lowers feasibility). Mechanical, data-driven, templated work scores higher.
- **Leverage** — 1_Low / 2_Medium / 3_High. The payoff if automated — how much lift/work-saved results. Driven by repeatability (happens every trade, same way), time saved, and importance to the trade's success. Leverage measures the *output's* value, independent of whether the AI build itself requires judgment.

### The 2x2 (for synthesis/discussion, not a table column)
- **High Feasibility + High Leverage** = Start Here
- **High Feasibility + Low Leverage** = Easy Win (low priority)
- **Low Feasibility + High Leverage** = Human-in-the-Loop (agent does mechanical part, pauses for human judgment, resumes)
- **Low Feasibility + Low Leverage** = No Way Jose

Low feasibility doesn't mean never — it means the workflow shape is agent → human checkpoint → agent resumes, rather than full autonomy.

---

## Source Material — Phases 1-3 (17 tasks total)

Three HTML files, sitting on Troy's computer (no special storage needed — drag into the session at the start):

- **Phase 1 — Awareness & Recruiting** (4 tasks): Venue Lock-In, Build the Trade Schedule, Facebook Announcement Post, Sing from the Mountain Tops
- **Phase 2 — Offer Up Games** (5 tasks): GeekList Creation & Launch, Discord Channel Setup, Advertising & Awareness, Monitoring GeekList Additions, Tracking & Encouraging Participation
- **Phase 3 — Wants Phase / Practice Runs** (8 tasks): Capture Practice Run Data, Update the Results Tracker, Formulate Results Communication, Upload Results to OLWLG, Send Results Communication, Perform Anomaly Review, Review Pending Results Summary, Monitoring & Nudging Submissions

Each task in these files already has a Description, Task Steps, and Why Do This — this is the raw material for Input/Process/Output. The IPO table is a re-projection of this existing content through a new lens (the two grades), not a re-derivation from scratch.

---

## Open Decisions (to be made in the build session)

- **Where the IPO table lives** — markdown table in chat/artifact, new Google Sheet, or something else.
- **Population pacing** — all 17 rows in one pass, or phase by phase with check-ins (similar to how Phases 1-3 themselves were built).

---

## Current Status

- Two-axis grading system (Feasibility / Leverage, 1/2/3 scale): locked
- Table column structure: locked
- Source material: complete and available (Phases 1-3 HTML, 17 tasks)
- Table population: not started — next step
- Timeline: 1-2 days to populate + prioritize, ahead of June 27 GeekList launch

---

*Created: June 14, 2026*
