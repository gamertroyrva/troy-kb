# Goal Card: ADS_MASTER Agentic Loop
*Backlog Item 13 — Andrew's Driving Services, Locations Database*
*Companion doc: `goal-card-session-restart_ads-master-agentic-loop.md` (architecture rationale — note: its scratch-copy description in §1/§5 is superseded by the Revision Cycle below)*
*Template used: `agentic-loop-goal-designer-prompt_v2.md`*
*This is v2 of this Goal Card, produced by a full Revision Cycle against v1 (9 items). This card is parameterized by rung — row count and STOP-CAPS change per invocation, everything else holds constant across all firings.*

---

## Terminology (established once, used freely below)

| Nickname used throughout | File | Repo path |
|---|---|---|
| **Operating Guide** | `ADS_RowBuilder_Operating_Guide.md` | `docs/ADS_RowBuilder_Operating_Guide.md` |
| **Data Dictionary** | `data-dictionary.xlsx` (incl. `Slug_Rules_Examples` tab) | `docs/data-dictionary.xlsx` |
| **Master** | `ads_master.xlsx` — the live working file this loop populates directly | `data/ads_master.xlsx` |
| **Answer Key** | `answer_key_ads_master.xlsx` — qa's integrity reference, 6 known-good rows | `data/answer_key_ads_master.xlsx` |
| **Audit Log** | `audit_log.xlsx` — 9-column log of every business-analyst resolution | `data/audit_log.xlsx` |

---

## 1. Goal Card (one page)

**OBJECTIVE**
Populate the Master's currently-unstarted venue rows (Venue Number 7 onward, 190 rows total) across all 70 fields, using a three-agent architecture (worker / qa / business-analyst) modeled on RowBuilder's existing research-and-population process, running fully autonomously per invocation.

**OUTPUT**
The Master, edited directly — there is no scratch or intermediate copy. This is Troy's active working file for the duration of this backlog item, not a production system with other live consumers. Recoverability is provided externally: Troy archives an interim copy of the Master after each rung he approves, entirely outside the loop's awareness. The loop has no responsibility for backups and makes no assumption one exists.
Companion: the Audit Log — one row per business-analyst resolution (see §4 of the restart doc for original spec; column set finalized in the Revision Cycle, see STAGES below).

**DONE WHEN** *(parameterized by N = this rung's row count)*
N of the 190 currently-unstarted rows in the Master are fully populated across all 70 fields — every cell resolved to one of the three sanctioned states (real data / `NOTFOUND — [reason]` / `N/A — [reason]`; Venue Name Aliases is the sole true-blank exception) — AND the 6 pre-completed rows (Venue Numbers 1–6) remain byte-identical to the Answer Key.

**QUALITY**
Follow the Operating Guide (v3.3) and Data Dictionary exactly: three-state blank-handling convention, Source References pipe-delimited convention, timestamp convention (nearest hour, ET, both fields identical).

On NOTFOUND specifically — two distinct things, kept distinct:
- **The normal path to NOTFOUND**: worker researches genuinely and concludes, across the sources actually available, that the information isn't out there (e.g., no TikTok presence for this venue, genuinely). This is the expected, common route to a NOTFOUND cell and requires no special handling — it's just what honest research sometimes finds.
- **Empty Fetch Discipline** is a narrower guardrail, not an alternate route to NOTFOUND: if a specific fetch or search *tool call* fails outright (error, empty result, no content), that's a hard stop for *that one attempt* — flag it, retry once if a retry path exists, then continue researching via other sources that were actually and successfully retrieved. Only mark NOTFOUND if no successful retrieval anywhere supports the field. Never fabricate plausible-sounding detail to paper over a failed fetch.

No agent in this loop makes content-quality judgment calls — that stays entirely manual, Troy's call at rope checkpoints, not any agent's, regardless of how sparse or thin a row's content turns out to be.

**CONTEXT**
- Operating Guide — all three agents
- Data Dictionary, including `Slug_Rules_Examples` tab — all three agents
- Master — worker (read/write, unstarted rows only) and qa (read, for verification)
- Answer Key — **qa only.** Worker and business-analyst have no path to it and no awareness it exists.
- Web search — worker (venue research) **and business-analyst** (grounding judgment calls in real information rather than pure inference — e.g., researching a categorization question or checking how a comparable venue handles a similar field)

**CONSTRAINTS**
- Worker: reads and writes the Master directly, scoped strictly to currently-unstarted rows (Venue Number 7 onward). Must never modify rows 2–7 (Venue Numbers 1–6, the pre-completed rows the Answer Key check depends on). Never accesses the Answer Key under any circumstance.
- Business-analyst: same Answer Key restriction as worker. Resolves worker's forks, returns an answer, logs to the Audit Log, never stalls the loop, never pages Troy.
- qa: read-only access to the Answer Key for the byte-identical check, and read access to the Master for verification. No write access anywhere. No opinion on content quality — structural/procedural completeness only.
- No agent writes to any file in the repo other than the Master (worker, unstarted rows only) and the Audit Log (worker/business-analyst, append-only). Nothing touches `docs/`, `.claude/`, `README.md`, or anything outside those two files.
- Archiving/backup of the Master between rungs is entirely Troy's responsibility, entirely outside the loop.

**STAGES**
1. **Preflight** — verify the Master and the Audit Log (with its headers already in place) both exist at their expected repo paths. If either is missing, **hard stop and flag it** — never silently create either file. (This replaces the earlier "create if missing" design from v1 of this Goal Card, dropped in the Revision Cycle in favor of Troy pre-creating both once, deliberately, with full visibility into that step.)
2. Worker executes Batches 1–7 per the Operating Guide **and Data Dictionary** for the next N unstarted rows, consulting business-analyst on genuine forks and logging each resolution to the Audit Log (columns: Entry #, Rung, Venue Number, _slug, Batch, Field, Question, Resolution, Timestamp).
3. qa checks: N target rows structurally complete + rows 2–7 in the Master still byte-identical to the Answer Key.
4. Report to Troy: turns used, wall-clock time used, Audit Log entries generated this run.

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

Paste this in Claude Code after subagent files are in place, `data/ads_master.xlsx` and `data/audit_log.xlsx` (headers only) are pre-created, and models are confirmed set (see reminders below):

```
/goal Using docs/ADS_RowBuilder_Operating_Guide.md (v3.3) and docs/data-dictionary.xlsx
(including the Slug_Rules_Examples tab) as the authoritative references, populate
1 row in data/ads_master.xlsx.

Preflight: confirm data/ads_master.xlsx and data/audit_log.xlsx both exist. If either
is missing, stop immediately and report - do not create either file.

Use the worker subagent to execute Batches 1-7 against the next currently-unstarted
row (Venue Number 7 onward, in order) in data/ads_master.xlsx, following the
three-state blank-handling convention (real data / NOTFOUND - [reason] / N/A -
[reason], Venue Name Aliases excepted) exactly as specified in the Operating Guide.
Worker must never modify rows 2-7 (Venue Numbers 1-6).

On NOTFOUND: the normal path is genuine research concluding the information isn't
available anywhere. Separately, if a specific fetch or search tool call fails outright,
treat that as a hard stop for that one attempt, retry once if possible, then continue
researching via other sources - only mark NOTFOUND if nothing successfully retrieved
supports the field. Never fabricate plausible detail to cover a failed fetch.

When worker hits a genuine fork it cannot resolve from the Data Dictionary and
Operating Guide alone, invoke the business-analyst subagent. Business-analyst may use
web search to ground its answer. Log every resolution to data/audit_log.xlsx with:
Entry #, Rung ("1 row"), Venue Number, _slug, Batch, Field, Question, Resolution,
Timestamp. Continue - never stop the loop to ask Troy.

Neither worker nor business-analyst may access data/answer_key_ads_master.xlsx under
any circumstance.

When the row is complete, invoke the qa subagent to verify: (1) all 70 fields in the
target row are resolved to one of the three sanctioned states, and (2) rows 2-7 in
data/ads_master.xlsx remain byte-identical to data/answer_key_ads_master.xlsx (qa has
read-only access to the Answer Key; no other subagent does).

DONE WHEN qa confirms both conditions above are true.

STOP if 6 consecutive turns pass with no measurable progress (no field populated,
no batch advanced, no business-analyst resolution returned), OR if 45 turns are
reached, OR if 45 minutes of wall-clock time elapse - whichever comes first. If
stopped, report exactly what was completed, what remains, and why it stopped.

On completion or stop, report: turns used, wall-clock time used, and all
data/audit_log.xlsx entries generated this run.
```

*(For rungs 2+, swap "1 row" → "the next 3 / 6 / 10... currently-unstarted rows," the Rung value in Audit Log entries, and the STOP-CAPS clause per the table in §1.)*

---

## 3. Manual Verification Checklist (run after each rung stops)

1. Open the Master — confirm the target row(s) exist and Venue Number sequence is unbroken (no skipped rows), and that rows 2–7 look untouched.
2. Spot-check 2–3 populated cells against what a quick manual web search turns up — confirms worker isn't fabricating despite Empty Fetch Discipline.
3. Confirm rows 2–7 are still byte-identical to the Answer Key (this should already be qa-verified, but a manual eyeball at low row-counts is cheap insurance).
4. Open the Audit Log — read every business-analyst resolution generated this run. This is your primary window into how business-analyst is actually reasoning.
5. Check `/status` reading from before/after this run — record the delta.
6. **Archive an interim copy of the Master** — this is Troy's responsibility, not the loop's; do it now, right after reviewing, before granting the next rung.
7. Decide: grant the next rung of rope, or hold and adjust something first.

---

## 4. Three Failure Modes to Watch

**Runaway tokens** — the run is burning turns fast without much to show for it (e.g., business-analyst getting invoked constantly, or worker retrying failed fetches in loops). *What to do:* let the stall/turn cap catch it (it's designed to), then read the Audit Log before the next rung — if business-analyst is firing on nearly every row, and the Audit Log's Batch/Field columns show a pattern (e.g., always Batch 5), the Data Dictionary or Operating Guide may need a clarifying update so worker stops hitting the same fork repeatedly.

**Endless/stuck loop** — qa never confirms DONE WHEN even though the row looks complete to you. *What to do:* check whether the Answer Key comparison is the culprit (did something in rows 2–7 actually drift?) versus a genuine gap in the target row's 70 fields. If it's a qa false-negative, that's a structural bug in the qa subagent's check logic worth fixing before continuing.

**Done-but-mediocre** — qa passes the row (structurally complete) but the content itself is thin or wrong. *What to do:* this is exactly what your manual review (checklist item 2) is for — qa was deliberately built with no opinion on quality, so this is caught by you, not the loop. If it happens often, that's a signal quality needs a sharper, checkable definition — not a reason to give qa opinions it wasn't designed to have.

---

## 5. Reminders

**Before firing anything:**
1. Set worker, qa, and business-analyst as three separate subagent files in `.claude/agents/`, each with its own `model` field in the YAML frontmatter (Sonnet 5 Medium / Sonnet 5 Low / Opus 5 High respectively) — this is not something the `/goal` command text itself configures.
2. Verify business-analyst's and worker's subagent definitions never reference or grant access to the Answer Key — only qa's definition should.
3. Pre-create `data/ads_master.xlsx` (already exists — just confirm it's the current version) and `data/audit_log.xlsx` (headers only, 9 columns) before rung 1. The loop will hard-stop and flag rather than proceed if either is missing — it will not create them for you.
4. Archiving the Master between rungs is your job, not the loop's. There's no scratch copy or in-loop safety net for this — do it deliberately, at each checkpoint, per the Verification Checklist above.
5. `data/audit_log.xlsx` should be gitignored (`data/audit_log.xlsx` line in `.gitignore`); the Master and Answer Key remain tracked in git as-is, giving you a second, independent recovery path underneath your own archiving.
6. Loops are expensive in tokens, can thrash on a weak done-condition, and can "pass" while still being low quality. The caps and the sharp DONE WHEN above are what make this safe to run unattended — don't loosen either without a specific reason.

**Final instruction (per the template):** wait for your explicit approval before firing rung 1.
