# Goal Card: ADS_MASTER Agentic Loop
*Backlog Item 13 — Andrew's Driving Services, Locations Database*
*Companion doc: `goal-card-session-restart_ads-master-agentic-loop.md` (architecture rationale)*
*Template used: `agentic-loop-goal-designer-prompt_v2.md`*
*This card is parameterized by rung — row count and STOP-CAPS change per invocation, everything else holds constant across all firings.*

---

## 1. Goal Card (one page)

**OBJECTIVE**
Populate ADS_MASTER's currently-unstarted venue rows (Venue Number 7 onward, 190 rows total) across all 70 fields, using a three-agent architecture (worker / qa / business-analyst) modeled on RowBuilder's existing research-and-population process, running fully autonomously per invocation.

**OUTPUT**
`ads_master_scratch.xlsx` — a full duplicate of `ads_master.xlsx` (all 197 data rows, not just the unstarted ones), created once on first firing and accumulated across subsequent rungs. The real `ads_master.xlsx` is never opened again after the initial duplication — not by worker, not by qa, not by business-analyst.
Companion: `audit_log.xlsx` — one row per business-analyst resolution (see §4 below), also created once and accumulated across rungs.

**DONE WHEN** *(parameterized by N = this rung's row count)*
N of the 190 currently-unstarted rows in `ads_master_scratch.xlsx` are fully populated across all 70 fields — every cell resolved to one of the three sanctioned states (real data / `NOTFOUND — [reason]` / `N/A — [reason]`; Venue Name Aliases is the sole true-blank exception) — AND the 6 pre-completed rows (Venue Numbers 1–6) remain byte-identical to `answer_key_ads_master.xlsx`.

**QUALITY**
Follow `ADS_RowBuilder_Operating_Guide.md` v3.3 and `data-dictionary.xlsx` exactly: three-state blank-handling convention, Empty Fetch Discipline (never fabricate on failed fetch — hard stop, retry once, else NOTFOUND), Source References pipe-delimited convention, timestamp convention (nearest hour, ET, both fields identical). No in-loop content-quality override — the years-in-operation-vs-thin-content judgment stays manual, Troy's call at rope checkpoints, not any agent's.

**CONTEXT**
- `ADS_RowBuilder_Operating_Guide.md` (v3.3) — all three agents
- `data-dictionary.xlsx`, including `Slug_Rules_Examples` tab — all three agents
- `ads_master.xlsx` — read once, at loop start, to create the scratch duplicate. Not reopened.
- `answer_key_ads_master.xlsx` — **qa only.** Worker and business-analyst have no path to it and no awareness it exists.
- Web search: yes, for worker's research per venue.

**CONSTRAINTS**
- Worker writes ONLY to `ads_master_scratch.xlsx` and (via business-analyst resolutions) `audit_log.xlsx`. Never opens `ads_master.xlsx` or `answer_key_ads_master.xlsx`.
- Business-analyst: same Answer Key restriction as worker. Resolves worker's forks, returns an answer, never stalls the loop, never pages Troy.
- qa: read-only access to `answer_key_ads_master.xlsx` for the byte-identical check. No write access anywhere. No opinion on content quality — structural/procedural completeness only.
- No agent writes to the real `ads_master.xlsx`, the `andrews-driving-services` repo's production files, or any shared/live system. Merging scratch progress back into the real master is manual, Troy-only, after rope-checkpoint review.

**STAGES**
1. Setup — if `ads_master_scratch.xlsx` doesn't yet exist, create it as a full duplicate of `ads_master.xlsx`; same for `audit_log.xlsx`. If both already exist (later rungs), use as-is.
2. Worker executes Batches 1–7 per the Operating Guide for the next N unstarted rows, consulting business-analyst on genuine forks.
3. qa checks: N target rows structurally complete + rows 2–7 still byte-identical to Answer Key.
4. Report to Troy: turns used, wall-clock time used, audit log entries generated this run.

**STOP-CAPS** *(rung-specific — table below; extend rightward as data comes in)*

| Rung | Rows | Turn cap | Time cap | Stall condition |
|---|---|---|---|---|
| 1 | 1 | 45 | 45 min | 6 consecutive turns, no measurable progress |
| 2 | 3 | 140 | 2 hrs | same |
| 3 | 6 | 280 | 4 hrs | same |
| 4 | 10 | *TBD from rungs 1–3 data* | *TBD* | same |
| 5–8 | 25 / 50 / 100 / 190 | *TBD, extrapolated* | *TBD* | same |

**MODEL** *(set via subagent definitions in `.claude/agents/`, not inline in the `/goal` text — see reminder below)*
- **worker** — Sonnet 5, Medium
- **qa** — Sonnet 5, Low
- **business-analyst** — Opus 5, High

---

## 2. The `/goal` Command (Rung 1 — 1 row)

Paste this in Claude Code after subagent files and models are confirmed set (see reminder below):

```
/goal Using ADS_RowBuilder_Operating_Guide.md (v3.3) and data-dictionary.xlsx
(including the Slug_Rules_Examples tab) as the authoritative references, populate
1 row in ads_master_scratch.xlsx.

If ads_master_scratch.xlsx does not exist, create it first as a full, byte-identical
duplicate of ads_master.xlsx (all 197 data rows) before doing anything else. Do not
open ads_master.xlsx again after this duplication step. Also create audit_log.xlsx
if it does not exist, with columns: Row identifier (Venue Number + slug), Question,
Resolution.

Use the worker subagent to execute Batches 1-7 against the next currently-unstarted
row (Venue Number 7 onward, in order) in ads_master_scratch.xlsx, following the
three-state blank-handling convention (real data / NOTFOUND - [reason] / N/A -
[reason], Venue Name Aliases excepted) and Empty Fetch Discipline exactly as
specified in the Operating Guide. When worker hits a genuine fork it cannot resolve
from the Data Dictionary and Operating Guide alone, invoke the business-analyst
subagent, log the question and resolution to audit_log.xlsx, and continue - never
stop the loop to ask Troy.

Neither worker nor business-analyst may access answer_key_ads_master.xlsx under any
circumstance.

When the row is complete, invoke the qa subagent to verify: (1) all 70 fields in the
target row are resolved to one of the three sanctioned states, and (2) rows 2-7 in
ads_master_scratch.xlsx remain byte-identical to answer_key_ads_master.xlsx (qa has
read-only access to the Answer Key; no other subagent does).

DONE WHEN qa confirms both conditions above are true.

STOP if 6 consecutive turns pass with no measurable progress (no field populated,
no batch advanced, no business-analyst resolution returned), OR if 45 turns are
reached, OR if 45 minutes of wall-clock time elapse - whichever comes first. If
stopped, report exactly what was completed, what remains, and why it stopped.

On completion or stop, report: turns used, wall-clock time used, and all audit_log.xlsx
entries generated this run.
```

*(For rungs 2+, swap "1 row" → "the next 3 / 6 / 10... currently-unstarted rows" and the STOP-CAPS clause per the table in §1.)*

---

## 3. Manual Verification Checklist (run after each rung stops)

1. Open `ads_master_scratch.xlsx` — confirm the target row(s) exist and Venue Number sequence is unbroken (no skipped rows).
2. Spot-check 2–3 populated cells against what a quick manual web search turns up — confirms worker isn't fabricating despite Empty Fetch Discipline.
3. Confirm rows 2–7 in the scratch file are still byte-identical to `answer_key_ads_master.xlsx` (this should already be qa-verified, but a manual eyeball at low row-counts is cheap insurance).
4. Open `audit_log.xlsx` — read every business-analyst resolution generated this run. This is your primary window into how business-analyst is actually reasoning (per your note — you genuinely don't know yet how it'll handle questions, and this is where you find out).
5. Check `/status` reading from before/after this run — record the delta.
6. Decide: grant the next rung of rope, or hold and adjust something first.

---

## 4. Three Failure Modes to Watch

**Runaway tokens** — the run is burning turns fast without much to show for it (e.g., business-analyst getting invoked constantly, or worker retrying failed fetches in loops). *What to do:* let the stall/turn cap catch it (it's designed to), then read the audit log before the next rung — if business-analyst is firing on nearly every row, the Data Dictionary or Operating Guide may need a clarifying update so worker stops hitting the same fork repeatedly.

**Endless/stuck loop** — qa never confirms DONE WHEN even though the row looks complete to you. *What to do:* check whether the Answer Key comparison is the culprit (did something in rows 2–7 actually drift?) versus a genuine gap in the target row's 70 fields. If it's a qa false-negative, that's a structural bug in the qa subagent's check logic worth fixing before continuing.

**Done-but-mediocre** — qa passes the row (structurally complete) but the content itself is thin or wrong. *What to do:* this is exactly what your manual review (checklist item 2, and your own years-in-operation judgment) is for — qa was deliberately built with no opinion on quality, so this is caught by you, not the loop. If it happens often, that's a signal quality needs a sharper, checkable definition — not a reason to give qa opinions it wasn't designed to have.

---

## 5. Reminders

**Before firing anything:**
1. Set worker, qa, and business-analyst as three separate subagent files in `.claude/agents/`, each with its own `model` field in the YAML frontmatter (Sonnet 5 Medium / Sonnet 5 Low / Opus 5 High respectively) — this is not something the `/goal` command text itself configures.
2. Verify business-analyst's and worker's subagent definitions never reference or grant access to `answer_key_ads_master.xlsx` — only qa's definition should.
3. Loops are expensive in tokens, can thrash on a weak done-condition, and can "pass" while still being low quality. The caps, the scratch-only sandbox, and the sharp DONE WHEN above are what make this safe to run unattended — don't loosen any of the three without a specific reason.

**Final instruction (per the template):** wait for your explicit approval before firing rung 1.
