# Goal Card: ADS_MASTER Agentic Loop
*Backlog Item 13 — Andrew's Driving Services, Locations Database*
*Companion doc: `goal-card-session-restart_ads-master-agentic-loop.md` (architecture rationale — its scratch-copy description in §1/§5 is superseded by the Revision Cycle in v2; still current beyond that)*
*Template used: `agentic-loop-goal-designer-prompt_v2.md`*
*This is v3 of this Goal Card, produced after Rung 1's first two attempts — attempt #1 (tool-capability gap, resolved same-session by granting Bash) and attempt #2 (DONE WHEN met, but surfaced three real infrastructure gaps, all fixed and validated before this version was written). This card is parameterized by rung — row count and STOP-CAPS change per invocation, everything else holds constant across all firings.*

---

## What changed in v3 (read this first if you read v2)

1. **Worker and business-analyst no longer write inline Python.** All Master/Audit Log reads and writes go through `scripts/ads_xlsx_tool.py`, invoked in one fixed, exact shape (see CONSTRAINTS). This replaces the wildcard `python -c` pattern that made settings.json impossible to scope safely.
2. **qa has its own dedicated script, `scripts/qa_verify_tool.py`**, separate from worker's/business-analyst's script on purpose — so the two subagents that must never touch the Answer Key never even have a script that mentions it.
3. **The Answer Key has moved outside the repo entirely**, to `C:/workspaces/non-repo/answer_key_ads_master.xlsx`. Worker and business-analyst's subagent files no longer reference this path at all. Isolation now works by withholding the information, not by trying to block access to information they already had — because a settings.json deny rule on `Read`/`Edit` does **not** stop a Bash-driven inline Python open of the same file, and Bash is granted to both. This was a genuine gap in v2, not a hypothetical one — discovered live during Rung 1 attempt #2.
4. **All three subagent files carry an explicit "stop and report, do not improvise" rule** for genuine tool-capability gaps, distinct from a worker fork (content judgment) or Empty Fetch Discipline (failed retrieval).
5. **The `/goal` command no longer hardcodes a starting row.** v2 said "Venue Number 7 onward" — accurate once, stale forever after. v3 says "the next currently-unstarted row, in Venue Number order," which never needs manual updating regardless of which row is actually next.
6. **New rung: Rung 1B**, a second single-row run inserted between Rung 1 (done) and Rung 2 (still 3 rows, unchanged). Rung 1's DONE WHEN was met on attempt #2, but every fix above was validated only by hand, never inside a live `/goal` run. Rung 1B exists to validate the fixes under real autonomous conditions, at the cheapest possible row-count, before trusting them at 3 rows. See STOP-CAPS table below — Rung 1B deliberately uses the *same* caps as Rung 1 (45 turns / 45 min), so turns-used and wall-clock become a clean, comparable measurement of whether today's fixes actually reduced overhead, not a new unknown baseline.

---

## Terminology (established once, used freely below)

| Nickname used throughout | File | Location |
|---|---|---|
| **Operating Guide** | `ADS_RowBuilder_Operating_Guide.md` | `docs/ADS_RowBuilder_Operating_Guide.md` |
| **Data Dictionary** | `data-dictionary.xlsx` (incl. `Slug_Rules_Examples` tab) | `docs/data-dictionary.xlsx` |
| **Master** | `ads_master.xlsx` — the live working file this loop populates directly | `data/ads_master.xlsx` |
| **Answer Key** | `answer_key_ads_master.xlsx` — qa's integrity reference, 6 known-good rows | `C:/workspaces/non-repo/answer_key_ads_master.xlsx` (moved outside the repo in v3 — see above) |
| **Audit Log** | `audit_log.xlsx` — 9-column log of every business-analyst resolution | `data/audit_log.xlsx` |
| **Worker's tool script** | `ads_xlsx_tool.py` — sanctioned read/write path for worker and business-analyst | `scripts/ads_xlsx_tool.py` |
| **qa's tool script** | `qa_verify_tool.py` — sanctioned check path for qa only; hard-codes the Answer Key path | `scripts/qa_verify_tool.py` |

---

## 1. Goal Card (one page)

**OBJECTIVE**
Populate the Master's currently-unstarted venue rows across all 70 fields, using a three-agent architecture (worker / qa / business-analyst) modeled on RowBuilder's existing research-and-population process, running fully autonomously per invocation.

**OUTPUT**
The Master, edited directly via `ads_xlsx_tool.py` — there is no scratch or intermediate copy. This is Troy's active working file for the duration of this backlog item, not a production system with other live consumers. Recoverability is provided externally: Troy archives an interim copy of the Master after each rung he approves, entirely outside the loop's awareness. The loop has no responsibility for backups and makes no assumption one exists.
Companion: the Audit Log — one row per business-analyst resolution, written via the same tool script.

**DONE WHEN** *(parameterized by N = this rung's row count)*
N of the currently-unstarted rows in the Master are fully populated across all 70 fields — every cell resolved to one of the three sanctioned states (real data / `NOTFOUND — [reason]` / `N/A — [reason]`; Venue Name Aliases is the sole true-blank exception) — AND rows 2–7 (Venue Numbers 1–6) remain byte-identical to the Answer Key.

**QUALITY**
Follow the Operating Guide (v3.3) and Data Dictionary exactly: three-state blank-handling convention, Source References pipe-delimited convention, timestamp convention (nearest hour, ET, both fields identical).

On NOTFOUND specifically — two distinct things, kept distinct:
- **The normal path to NOTFOUND**: worker researches genuinely and concludes, across the sources actually available, that the information isn't out there. This is the expected, common route to a NOTFOUND cell and requires no special handling.
- **Empty Fetch Discipline** is a narrower guardrail, not an alternate route to NOTFOUND: if a specific fetch or search *tool call* fails outright, that's a hard stop for *that one attempt* — flag it, retry once if a retry path exists, then continue researching via other sources that were actually and successfully retrieved. Only mark NOTFOUND if no successful retrieval anywhere supports the field. Never fabricate plausible-sounding detail to paper over a failed fetch.

No agent in this loop makes content-quality judgment calls — that stays entirely manual, Troy's call at rope checkpoints, not any agent's.

**CONTEXT**
- Operating Guide — all three agents
- Data Dictionary, including `Slug_Rules_Examples` tab — all three agents
- Master — worker (read/write, unstarted rows only, via `ads_xlsx_tool.py`) and qa (read, via `qa_verify_tool.py`)
- Answer Key — **qa only, via `qa_verify_tool.py` exclusively.** Worker and business-analyst have no path to it, no awareness of its location, and no reference to it anywhere in their subagent files.
- Web search — worker (venue research) **and business-analyst** (grounding judgment calls in real information)

**CONSTRAINTS**
- Worker and business-analyst: all Master/Audit Log reads and writes go through `scripts/ads_xlsx_tool.py`, invoked in **exactly** this shape — run from repo root, no `cd` prefix, no command chaining (`&&`, `;`, pipes), no output redirects. This is not a style preference: `.claude/settings.json`'s allow rule matches this exact shape as a literal prefix. A deviating call — even one that's otherwise safe and correct — will fall through to a live permission prompt, which is tolerable during an attended run and fatal to an unattended one.
- Worker: scoped strictly to currently-unstarted rows. Must never modify rows 2–7 (Venue Numbers 1–6) — `ads_xlsx_tool.py` also refuses these rows independently, as a second backstop beyond the prompt instruction.
- Business-analyst: same Answer Key non-awareness as worker. Resolves worker's forks, returns an answer, logs to the Audit Log via `ads_xlsx_tool.py`, never stalls the loop, never pages Troy.
- qa: all checks go through `scripts/qa_verify_tool.py`, in the same exact invocation shape. No write access anywhere. No opinion on content quality — structural/procedural completeness only.
- No agent writes to any file in the repo other than the Master and the Audit Log (both only via the sanctioned scripts). Nothing touches `docs/`, `.claude/`, `README.md`, or anything outside those two files.
- **Tool-capability gap ≠ fork.** If any agent genuinely lacks the tool or access to do something the task requires — not a missing data source, not an ambiguous rule, the wrong or missing tool itself — it must stop and report immediately rather than improvise a workaround, an alternate script, or an unapproved tool. This was added in v3 after Rung 1 attempt #1 came close to routing around a real gap on its own.
- Archiving/backup of the Master between rungs is entirely Troy's responsibility, entirely outside the loop.

**STAGES**
1. **Preflight** — verify the Master and the Audit Log (with its headers already in place) both exist at their expected repo paths, and that both tool scripts (`scripts/ads_xlsx_tool.py`, `scripts/qa_verify_tool.py`) exist. If anything is missing, **hard stop and flag it** — never silently create any of it.
2. Worker executes Batches 1–7 per the Operating Guide **and Data Dictionary** for the next N currently-unstarted rows, **in Venue Number order** — no row number is ever hardcoded into the invocation; worker determines the next unstarted row itself each time — consulting business-analyst on genuine forks and logging each resolution to the Audit Log.
3. qa checks, via `qa_verify_tool.py`: N target rows structurally complete + rows 2–7 in the Master still byte-identical to the Answer Key.
4. Report to Troy: turns used, wall-clock time used, Audit Log entries generated this run.

**STOP-CAPS** *(rung-specific — table below; extend rightward as data comes in)*

| Rung | Rows | Turn cap | Time cap | Stall condition |
|---|---|---|---|---|
| 1 | 1 | 45 | 45 min | 6 consecutive turns, no measurable progress |
| **1B** | **1** | **45** | **45 min** | **same — deliberately identical to Rung 1, to isolate whether today's fixes reduced turns/time, not to test a new scale** |
| 2 | 3 | 140 | 2 hrs | same |
| 3 | 6 | 280 | 4 hrs | same |
| 4 | 10 | *TBD from rungs 1–3 data* | *TBD* | same |
| 5–8 | 25 / 50 / 100 / 190 | *TBD, extrapolated* | *TBD* | same |

**MODEL** *(set via subagent definitions in `.claude/agents/`, not inline in the `/goal` text)*
- **worker** — Sonnet 5, Medium
- **qa** — Sonnet 5, Low
- **business-analyst** — Opus 5, High

---

## 2. The `/goal` Command (Rung 1B — 1 row, validation run)

Paste this in Claude Code, from a **fresh session** (not a continuation of any prior conversation — cached subagent definitions from an earlier session will not see today's `.claude/agents/*.md` edits):

```
/goal Using docs/ADS_RowBuilder_Operating_Guide.md (v3.3) and docs/data-dictionary.xlsx
(including the Slug_Rules_Examples tab) as the authoritative references, populate
1 row in data/ads_master.xlsx.

Preflight: confirm data/ads_master.xlsx, data/audit_log.xlsx, scripts/ads_xlsx_tool.py,
and scripts/qa_verify_tool.py all exist. If any is missing, stop immediately and
report - do not create any of them.

Use the worker subagent to execute Batches 1-7 against the next currently-unstarted
row, in Venue Number order, in data/ads_master.xlsx, following the three-state
blank-handling convention (real data / NOTFOUND - [reason] / N/A - [reason], Venue
Name Aliases excepted) exactly as specified in the Operating Guide. Worker must never
modify rows 2-7 (Venue Numbers 1-6). All reads and writes to data/ads_master.xlsx and
data/audit_log.xlsx must go through scripts/ads_xlsx_tool.py, invoked in the exact
shape specified in worker.md - no cd prefix, no chaining, no redirects.

On NOTFOUND: the normal path is genuine research concluding the information isn't
available anywhere. Separately, if a specific fetch or search tool call fails outright,
treat that as a hard stop for that one attempt, retry once if possible, then continue
researching via other sources - only mark NOTFOUND if nothing successfully retrieved
supports the field. Never fabricate plausible detail to cover a failed fetch.

When worker hits a genuine fork it cannot resolve from the Data Dictionary and
Operating Guide alone, invoke the business-analyst subagent. Business-analyst may use
web search to ground its answer, and logs every resolution to data/audit_log.xlsx via
scripts/ads_xlsx_tool.py. Continue - never stop the loop to ask Troy.

If either worker or business-analyst encounters a genuine tool-capability gap - not a
research fork, the wrong or missing tool itself - stop and report immediately rather
than improvising a workaround.

When the row is complete, invoke the qa subagent to verify, via
scripts/qa_verify_tool.py: (1) all 70 fields in the target row are resolved to one of
the three sanctioned states, and (2) rows 2-7 in data/ads_master.xlsx remain
byte-identical to the Answer Key.

DONE WHEN qa confirms both conditions above are true.

STOP if 6 consecutive turns pass with no measurable progress, OR if 45 turns are
reached, OR if 45 minutes of wall-clock time elapse - whichever comes first. If
stopped, report exactly what was completed, what remains, and why it stopped.

On completion or stop, report: turns used, wall-clock time used, and all
data/audit_log.xlsx entries generated this run.
```

*(For Rung 2+, swap "1 row" → "the next 3 / 6 / 10... currently-unstarted rows," the Rung value in Audit Log entries, and update the STOP-CAPS clause per the table in §1. Nothing else needs to change — the "next currently-unstarted row, in Venue Number order" phrasing already scales to any N.)*

---

## 3. Manual Verification Checklist (run after each rung stops)

1. Open the Master — confirm the target row(s) exist and Venue Number sequence is unbroken (no skipped rows), and that rows 2–7 look untouched.
2. Spot-check 2–3 populated cells against human knowledge and judgment — confirms worker isn't fabricating despite Empty Fetch Discipline.
3. Run `python scripts/qa_verify_tool.py compare-answer-key` yourself as a manual double-check, even though qa already ran it — cheap insurance, especially at low row-counts.
4. Open the Audit Log — read every business-analyst resolution generated this run.
5. Check `/usage` reading (session-0 and weekly_all) from before/after this run — record the delta. This is real Measurement Plan data, not optional bookkeeping.
6. **Archive an interim copy of the Master** — Troy's responsibility, not the loop's; do it now, right after reviewing, before granting the next rung.
7. Decide: grant the next rung of rope, or hold and adjust something first.

---

## 4. Three Failure Modes to Watch

**Runaway tokens** — the run is burning turns fast without much to show for it. *What to do:* let the stall/turn cap catch it, then read the Audit Log before the next rung — if business-analyst is firing constantly with a pattern in the Batch/Field columns, the Data Dictionary or Operating Guide may need a clarifying update.

**Endless/stuck loop** — qa never confirms DONE WHEN even though the row looks complete. *What to do:* check whether the Answer Key comparison is the culprit (did rows 2–7 actually drift?) versus a genuine gap in the target row's 70 fields. If it's a qa false-negative, that's a structural bug in `qa_verify_tool.py` worth fixing before continuing.

**Done-but-mediocre** — qa passes the row structurally but the content itself is thin or wrong. *What to do:* this is exactly what manual review (checklist item 2) is for — qa was deliberately built with no opinion on quality.

---

## 5. Reminders

**Before firing anything:**
1. Confirm all three subagent files (`worker.md`, `qa.md`, `business-analyst.md`) reflect v3's changes: the fixed invocation shape, the tool-capability-gap stop-and-report rule, and — for worker/business-analyst — no reference anywhere to the Answer Key's existence or location.
2. Confirm `.claude/settings.json` matches the exact invocation shape both scripts require. A rule that matched an earlier, looser shape will silently stop protecting you.
3. Confirm `scripts/ads_xlsx_tool.py` and `scripts/qa_verify_tool.py` both exist and were tested manually at least once against the real Master and Answer Key before trusting them inside a live run.
4. Confirm the Answer Key genuinely lives at `C:/workspaces/non-repo/answer_key_ads_master.xlsx` and `data/answer_key_ads_master.xlsx` inside the repo no longer exists (a leftover copy in the old location would silently defeat the whole relocation).
5. Fire from a **fresh Claude Code session** — not a continuation of any session that predates today's subagent-file edits.
6. Archiving the Master between rungs is your job, not the loop's.
7. Loops are expensive in tokens, can thrash on a weak done-condition, and can "pass" while still being low quality. The caps and the sharp DONE WHEN above are what make this safe to run unattended — don't loosen either without a specific reason.

**Final instruction (per the template):** wait for your explicit approval before firing Rung 1B.
