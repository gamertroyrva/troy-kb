# Restart Doc: ADS_MASTER Agentic Loop — Advisory Session Orientation
*Generic and reusable — this doc is deliberately NOT versioned or dated, and does not need editing between resume cycles. It contains only things that stay true across many rungs. Anything that changes rung to rung (current status, what's pending, what just got fixed) belongs in the Goal Card's own "What changed in vN" section and the Issues Log's most recent entries — not here. If you ever find yourself wanting to add a status update to this doc, that's the signal to put it in one of those two places instead.*

---

## 1. The Big Picture

Backlog Item 13: populate the currently-unstarted rows of Andrew's Driving Services' locations database (`data/ads_master.xlsx`, 70 fields per venue), using a three-agent architecture (worker / qa / business-analyst).

The little-picture deliverable is the populated data. **The big-picture purpose is Troy's own hands-on education in agentic loops as a technique — this project is the real-world guinea pig for that learning, not the point in itself.** This isn't stated anywhere in the Goal Card, which only covers the operational objective. It matters for judgment calls: measurement-plan data (turns, wall-clock, `/usage` deltas across rungs) is not optional bookkeeping, it's the actual point of a lot of this exercise. A STOP-CAP overrun or a noisy run is often more interesting as a data point than as a problem to fix immediately.

## 2. For Current Status — Read These Fresh, Every Time

This doc will not tell you what's currently done, pending, or broken. That lives in exactly two places:
- The **current Goal Card**'s own version header and "What changed in vN" section (it tracks its own revision history — read the newest entry, and skim the superseded ones below it for context if needed).
- The **Issues Log**'s most recent entries (`AgenticLoop_IssuesLog.xlsx`, sorted by Entry #, with a `Status` column as of the entry where that column was introduced — check the highest-numbered entries first).

If Troy hands you a restart moment, those two sources are the ground truth for "where things stand," not this document and not your memory of a prior session.

## 3. Attach Alongside This Doc When Resuming

No versions or dates listed here on purpose — bring whatever the current copies are:
- The current Goal Card (`goal-card_ads-master-agentic-loop.md`)
- `AgenticLoop_IssuesLog.xlsx`
- `deferred-design-decisions_ads-agentic-loop.md`
- `ADS_RowBuilder_Operating_Guide.md`
- `data-dictionary.xlsx`
- `spreadsheet-style-preferences.md`

## 4. Small Things Worth Knowing

- Troy sometimes says **"wrong"** when he means **"rung"** — a voice-capture artifact, not a real word choice.
- **"Relitigate"** and **"signal"** are unwelcome words, per `anti-ai-writing-style.md` — that file is the source of truth, this is just a reminder it exists.
- **Advisory conversations happen live, throughout a run** — not as a batch review afterward. The only thing genuinely deferred to after a run ends is actually *implementing* infrastructure changes (editing settings, subagent files, tool scripts); discussing and deciding on them happens continuously as things come up. An earlier convention called this batch-review pattern "Tuesday Morning Quarterbacking" — that name is retired; the live-conversation model replaced it.
- **Deferred Decision #1** (whether worker/business-analyst get Bash access directly, vs. a narrower purpose-built mechanism) is a standing open decision, not settled fact — check `deferred-design-decisions_ads-agentic-loop.md` for its current state. As of August 16, 2026, walk-away unattended runs (same terminal, Troy physically absent) have been analyzed and accepted; the current trigger for reconsideration is a future headless/scheduled invocation mechanism, not a specific row count.
- The Goal Card and this restart doc are companions with different jobs: the Goal Card is the technical spec (objective, constraints, the actual `/goal` command text) and is versioned/superseded every time something changes. This doc is orientation and doesn't change nearly as often — if you're tempted to make this doc do the Goal Card's job, don't.
