# Goal Card: ADS_MASTER Agentic Loop
*Backlog Item 13 — Andrew's Driving Services, Locations Database*
*Companion doc: `advisory-session-restart_ads-master-agentic-loop.md` (architecture rationale — its scratch-copy description in §1/§5 is superseded by the Revision Cycle in v2; still current beyond that)*
*Template used: `agentic-loop-goal-designer-prompt_v2.md`*
*This is v12 of this Goal Card. Supersedes v11.*

---

## What changed in v12 (read this first if you read v11)

1. **Final report now includes WebSearch calls used**, alongside turns and wall-clock. Rung 8B (Aug 20, 2026) hard-stopped at row 22/25 when the session's WebSearch budget hit 200/200 — correctly handled as a tool-capability-gap Explicit Hard-Stop, but the report format had no place to surface that number explicitly. STAGES §3 and the `/goal` command's closing report line both updated. See Issues Log Entry 40 (troy-kb).

---

## What changed in v11 (read this first if you read v10)

1. **New Preflight check: Bash cwd resolution.** Rung 8A (Aug 20, 2026) found the shell's actual working directory had silently resolved to the repo's *parent* folder (`C:\workspaces` instead of `C:\workspaces\andrews-driving-services`) — a documented Claude Code Desktop bug on Windows (GitHub issue #52165), not anything in this repo. This broke every relative-path Bash invocation (`python scripts/...`) the moment a subagent tried one. Critically, the existing missing-file Preflight check (via Read/Glob) passed anyway — Read/Glob can resolve correctly even when Bash's own cwd is wrong, so it gave no warning. Preflight now independently confirms Bash itself is rooted at the repo, via a harmless `python scripts/ads_xlsx_tool.py --help` call, before any subagent is dispatched. New Explicit Hard-Stop trigger; see STAGES §1 and STOP-CAPS.
2. This also closes a process gap the same incident surfaced: on discovering the bug mid-rung (row 1 of 2), the orchestrator told qa to "proceed however necessary to get the checks run" rather than treating it as the tool-capability-gap stop-and-report case this document already requires — and qa then improvised two different unsanctioned invocation shapes across its two rows, both of which needed a live permission decision. Catching the problem once at Preflight, before any subagent runs, removes the situation where that call has to be made mid-row at all.
3. See Issues Log Entry 37 for the full incident (troy-kb).

---

## What changed in v10 (read this first if you read v9)

1. **STOP-CAPS section replaced.** The fixed per-rung table (planned row counts, caps, "TBD" placeholders for future rungs) is gone. Turn cap and time cap are now computed fresh per invocation from N, using a formula with a floor (see §1) — no more fixed row-count schedule. Historical rung sizes/caps/actuals now live only in Troy's own tracking spreadsheet, not duplicated here.
2. **The `/goal` command text is now a genuine reusable template.** Row count and both caps are `[N]` / `[turn_cap]` / `[time_cap]` placeholders, filled in at fire-time using §1's formula — not hardcoded to whichever rung fired most recently. This is also why the Goal Card should need far fewer version bumps going forward: most prior versions (v4–v9) existed mainly to update a row count.
3. **Time cap is now always expressed in minutes**, never hours — eliminates the inconsistency where some rungs' caps were written in minutes and others in hours.
4. §2's header no longer names a specific rung ("Rung 2 — 3 rows...") since it's a template, not a one-off.
5. The obsolete "For Rung 3+, swap..." footnote is removed — no longer needed once real placeholders exist.

---

## What changed in v9 (read this first if you read v8)

1. **Terminology table's Audit Log description dropped its column count.** Was "9-column log of every business-analyst resolution"; now just "log of every business-analyst resolution." Prompted by the Rung column's removal from the Audit Log schema (see Issues Log) — a hardcoded count in this table would have needed editing again the next time the schema changes, so the count is dropped rather than corrected.

---

## What changed in v8 (read this first if you read v7)

1. **Relocated.** This Goal Card (and the Issues Log, deferred-decisions doc, and restart doc) now live in `troy-kb/projects/ads-agent-ops/`, not `andrews-driving-services`. See Issues Log for the full incident.
2. **`/goal` command text tightened to fit the 4,000-character limit**, discovered live when Rung 2 attempt #2 was fired with the untightened v7 text (4,323 characters) and rejected outright. No rule was cut — only wordy phrasing. See Issues Log for the full incident.

---

## What changed in v7 (superseded by v8 above, kept for history)

1. **New fourth STOP-CAPS condition: Explicit Hard-Stop.** Alongside turn cap, time cap, and stall, a fourth condition is now named explicitly: any hard-stop authorized elsewhere in this goal's own text (the preflight missing-file clause, the tool-capability-gap rule) is treated as **met immediately** the moment it's triggered — not something to hold pending the stall counter. This is a standing rule, not rung-specific — it applies identically at every row count, unlike the turn/time caps which scale with N. Both places in this document that describe a hard-stop (STAGES §1 preflight, the tool-capability-gap CONSTRAINT) now cross-reference this explicitly, and the `/goal` command text's STOP clause carries it as a fourth named OR condition.

---

## What changed in v6 (superseded by v7 above, kept for history)

1. **Preflight guidance now warns that a Glob "not found" result is not conclusive on its own.** Rung 2 attempt #1 saw the orchestrator conclude `scripts/qa_verify_tool.py` didn't exist, based solely on Glob returning no matches — when the file was genuinely present the whole time. Root cause: Glob silently omits a file blocked by a `settings.json` deny rule from its results, rather than surfacing the denial, which looks identical to the file not existing at all. (This particular file is denied to `Read` on purpose, per v4's Answer-Key isolation fix — the deny rule was working exactly as designed; Glob's handling of it was the actual problem.) The preflight instruction now says: if Glob reports a file missing, confirm with `Read` before treating that as a real missing-file condition — an explicit "denied by permission settings" result from `Read` means the file exists; only a genuine not-found from `Read` is a true miss.

---

## What changed in v5 (superseded by v6 above, kept for history)

1. **New per-row worker→qa loop for multi-row rungs**, replacing the previously-unspecified "worker completes all N rows, then qa checks once at the end" assumption. STAGES now says explicitly: for each target row, invoke worker fresh — never handed more than one row per invocation — then invoke qa immediately on that one row, then hard-stop the entire rung on any qa failure (no skip, no retry-and-continue), then proceed to the next row only if qa passed. This threads two goals that looked like they were in tension: bounding worker's context growth at high row counts (25/50/100-row rungs) by never letting one session accumulate that much research history, while still catching a systemic problem at the smallest possible blast radius, since qa's checks are cheap regardless of rung size and there's no real cost to running them N times instead of once.
2. **`dump-audit-log` wired into the final reporting step** (Issues Log Entry 19). The orchestrator's closing report now explicitly calls `scripts/ads_xlsx_tool.py dump-audit-log` rather than leaving the retrieval mechanism unspecified — the exact gap that caused the orchestrator to improvise raw Python during Rung 1C's own final report.
3. **New CONSTRAINT** codifying #1 declaratively, alongside the existing ordering and isolation constraints — this repo's established pattern of stating important rules both procedurally (in STAGES) and as a flat "must never" (in CONSTRAINTS).
4. **STOP-CAPS clarified as cumulative** across a rung's full per-row loop, not reset per row. Previously ambiguous by omission; now stated explicitly.
5. **Incidental fix:** the `/goal` command text's Operating Guide version reference corrected from v3.3 to v3.4, matching the version that has actually been in effect since v4 introduced the Price Field Convention.

---

## What changed in v4 (superseded by v5 above, kept for history)

1. **New sanctioned action: `next-unstarted`**, added to `ads_xlsx_tool.py`. Worker no longer has to determine the next unstarted row through its own improvised reasoning — it calls this action directly. See the new CONSTRAINT line below for the ordering rule this depends on.
2. **New CONSTRAINT: strict top-to-bottom, ascending Venue Number ordering.** The Master fills in that order only — no skipping, no going back, ever. This is what makes `next-unstarted`'s lightweight single-field check (first row where `Last Verified/Updated` is blank) safe to rely on rather than merely convenient.
3. **`Read(scripts/qa_verify_tool.py)` added to settings.json's deny list.** Rung 1B surfaced a second Answer Key isolation vector beyond the one v3 closed — worker read the script's source directly (not executed it) while hunting for a listing capability, and the source hard-codes the Answer Key's path. Closed at the settings.json layer, and structurally prevented going forward since worker now has no reason to look (see #1).
4. **New `hooks.PreToolUse` block in settings.json**, two matcher entries: one for `WebSearch|WebFetch` (neither tool can be reliably auto-approved via `permissions.allow` in current Claude Code — WebSearch has no config path at all, WebFetch's domain wildcard is separately buggy), one for `Bash` (the `*` wildcard fails to match commands containing double-quote characters, a documented Claude Code bug, which was silently defeating the sanctioned-script allow rule on nearly every quoted argument). Neither hook widens what's actually permitted — both re-implement the same narrow intent the broken config mechanism was supposed to enforce. Validated live by Rung 1C — turns and wall-clock both dropped sharply versus Rung 1B, strong evidence both hooks worked as intended.
5. **New Price Field Convention** (Operating Guide v3.4): price values write as plain numbers, no currency symbol (`25/person`, not `$25/person`). A literal `$` in a `--value` argument triggers shell variable-expansion behavior and a permission prompt even when escaped.
6. **Wall-clock STOP-CAP observed exceeded without stopping** (~52 min against a 45-min cap, Rung 1B). Noted, not investigated, not fixed this cycle — Troy's explicit call. Rung 1C finished well inside its cap (~21 min against 45), so this has not recurred, but the underlying mechanism question (prompt-language cap vs. code-enforced cap) remains open.

---

## What changed in v3 (superseded by v4 above, kept for history)

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
| **Audit Log** | `audit_log.xlsx` — log of every business-analyst resolution | `data/audit_log.xlsx` |
| **Worker's tool script** | `ads_xlsx_tool.py` — sanctioned read/write path for worker and business-analyst, and the orchestrator's own `dump-audit-log` reporting step | `scripts/ads_xlsx_tool.py` |
| **qa's tool script** | `qa_verify_tool.py` — sanctioned check path for qa only; hard-codes the Answer Key path | `scripts/qa_verify_tool.py` |

---

## 1. Goal Card (one page)

**OBJECTIVE**
Populate the Master's currently-unstarted venue rows across all 70 fields, using a three-agent architecture (worker / qa / business-analyst) modeled on RowBuilder's existing research-and-population process, running fully autonomously per invocation.

**OUTPUT**
The Master, edited directly via `ads_xlsx_tool.py` — there is no scratch or intermediate copy. This is Troy's active working file for the duration of this backlog item, not a production system with other live consumers. Recoverability is provided externally: Troy archives an interim copy of the Master after each rung he approves, entirely outside the loop's awareness. The loop has no responsibility for backups and makes no assumption one exists.
Companion: the Audit Log — one row per business-analyst resolution, written via the same tool script.

**DONE WHEN** *(parameterized by N = this rung's row count)*
N of the currently-unstarted rows in the Master are fully populated across all 70 fields — every cell resolved to one of the three sanctioned states (real data / `NOTFOUND — [reason]` / `N/A — [reason]`; Venue Name Aliases is the sole true-blank exception) — AND rows 2–7 (Venue Numbers 1–6) remain byte-identical to the Answer Key, confirmed by qa immediately after each row completes, not once at the end of the rung. Any qa failure on any row stops the rung entirely — DONE WHEN is only met if every one of the N target rows individually passed both checks.

**QUALITY**
Follow the Operating Guide (v3.4) and Data Dictionary exactly: three-state blank-handling convention, Source References pipe-delimited convention, timestamp convention (nearest hour, ET, both fields identical).

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
- **The Master fills strictly in ascending Venue Number order — top to bottom, no skipping, no going back, ever.** Worker determines the next unstarted row by calling `ads_xlsx_tool.py next-unstarted`, which returns the first row (in that order) where `Last Verified/Updated` is blank. This ordering rule is what makes that lightweight check safe rather than merely convenient — see Operating Guide v3.4, Row Ordering & Next-Unstarted Convention.
- **Worker is invoked fresh once per target row — never handed multiple rows in a single invocation.** qa verifies each row immediately after worker completes it, not once at the end of the rung. Any qa failure on any row is an immediate hard stop for the entire rung; there is no skip, no retry-and-continue past a failed row.
- Worker and business-analyst: all Master/Audit Log reads and writes, and all Data Dictionary reads, go through `scripts/ads_xlsx_tool.py`, invoked in **exactly** this shape — run from repo root, no `cd` prefix, no command chaining (`&&`, `;`, pipes), no output redirects. This is not a style preference: `.claude/settings.json`'s allow rule matches this exact shape as a literal prefix. A deviating call — even one that's otherwise safe and correct — will fall through to a live permission prompt, which is tolerable during an attended run and fatal to an unattended one.
- Worker: scoped strictly to currently-unstarted rows. Must never modify rows 2–7 (Venue Numbers 1–6) — `ads_xlsx_tool.py` also refuses these rows independently, as a second backstop beyond the prompt instruction.
- Business-analyst: same Answer Key non-awareness as worker. Resolves worker's forks, returns an answer, logs to the Audit Log via `ads_xlsx_tool.py`, never stalls the loop, never pages Troy.
- qa: all checks go through `scripts/qa_verify_tool.py`, in the same exact invocation shape. No write access anywhere. No opinion on content quality — structural/procedural completeness only.
- No agent writes to any file in the repo other than the Master and the Audit Log (both only via the sanctioned scripts). Nothing touches `docs/`, `.claude/`, `README.md`, or anything outside those two files.
- **Tool-capability gap ≠ fork.** If any agent genuinely lacks the tool or access to do something the task requires — not a missing data source, not an ambiguous rule, the wrong or missing tool itself — it must stop and report immediately rather than improvise a workaround, an alternate script, or an unapproved tool. This was added in v3 after Rung 1 attempt #1 came close to routing around a real gap on its own. This is also the Explicit Hard-Stop STOP-CAPS condition (see below) — met immediately, not held pending the stall counter.
- Archiving/backup of the Master between rungs is entirely Troy's responsibility, entirely outside the loop.

**STAGES**
1. **Preflight** — verify the Master and the Audit Log (with its headers already in place) both exist at their expected repo paths, and that both tool scripts (`scripts/ads_xlsx_tool.py`, `scripts/qa_verify_tool.py`) exist. If a Glob check reports a file missing, confirm with `Read` before treating that as real — Glob silently omits files blocked by a permission deny rule rather than flagging the denial, which looks identical to "doesn't exist" (see v6 changelog). A `Read` result of "denied by permission settings" means the file exists; only a genuine not-found from `Read` is a true miss. Separately, confirm **Bash itself** is rooted at the repo — run `python scripts/ads_xlsx_tool.py --help` via Bash; an error instead of usage text means Bash's working directory isn't the repo root, even though the Read/Glob check above passed. These are independent checks on purpose: Read/Glob can resolve correctly even when Bash's own cwd is wrong (see v11 changelog, Rung 8A), so a passing file-existence check does not vouch for Bash. If either check fails, **hard stop and flag it** — never silently create a missing file, and never `cd`, never fall back to an absolute path, never let any subagent improvise a workaround around a cwd problem. This is the Explicit Hard-Stop STOP-CAPS condition (see below) — met immediately, not held pending the stall counter.
2. **For each of the N target rows, in order:**
   1. Invoke the **worker** subagent, fresh for this row — never handed more than one row per invocation — to execute Batches 1–7 per the Operating Guide and Data Dictionary against the next currently-unstarted row, determined by calling `ads_xlsx_tool.py next-unstarted` (never hardcoded, never independent reasoning), consulting **business-analyst** on genuine forks and logging each resolution to the Audit Log.
   2. Invoke **qa** immediately on that one row, via `qa_verify_tool.py`: the row is structurally complete (all 70 fields resolved to a sanctioned state), and rows 2–7 in the Master remain byte-identical to the Answer Key.
   3. If qa fails either check: **hard stop the entire rung immediately.** Do not proceed to any further rows, even if this was not the last of the N target rows. Report exactly what completed, what failed, and why. This is also the Explicit Hard-Stop STOP-CAPS condition (see below) — met immediately, not held pending the stall counter.
   4. If qa passes: continue to the next of the N target rows and repeat from (1).
3. Report to Troy: turns used, wall-clock time used, WebSearch calls used, all Audit Log entries generated this run — retrieved via `ads_xlsx_tool.py dump-audit-log`, never via inline Python.

**STOP-CAPS**

Four conditions stop a rung, not three. Three are computed fresh for each invocation from N (that rung's row count); the fourth is a standing rule, identical at every N:

- **Turn cap** — multiply N by 2, add 13, then round up to the next multiple of 5. If that's less than 40, use 40.
- **Time cap** — multiply N by 13.2, then round up to the next multiple of 30. That's your cap in minutes — never switch to hours. If it's less than 120, use 120.
- **Stall condition** — 6 consecutive turns, no measurable progress.
- **Explicit Hard-Stop** *(cwd trigger added in v11, otherwise unchanged since v7)* — any hard-stop this document authorizes elsewhere (the preflight missing-file clause, the preflight Bash-cwd-resolution check, the tool-capability-gap rule, a qa failure) is met **immediately** when triggered. It does not wait for the stall condition to separately catch up. Before v7, only turn cap / time cap / stall were mechanically recognized as legitimate reasons to end a turn — a prompt-level "stop immediately" instruction wasn't one of them, which meant an already-authorized, correct stop (Rung 2 attempt #1's preflight failure) still had to grind through several extra turns before the stall condition let it actually take effect. This condition exists so that no longer happens.

Caps apply cumulatively across the rung's full per-row loop, not per row. N and its caps are set fresh at the start of each invocation, based on the time available that sitting — no fixed row-count schedule. History (past rung sizes, caps, and actuals) lives in Troy's own tracking spreadsheet, not here.

**MODEL** *(set via subagent definitions in `.claude/agents/`, not inline in the `/goal` text)*
- **worker** — Sonnet 5, Medium
- **qa** — Sonnet 5, Low
- **business-analyst** — Opus 5, High

---

## 2. The `/goal` Command (template — fill in [N], [turn_cap], [time_cap] each time, using §1's formula)

Paste this in Claude Code, from a **fresh session** (not a continuation of any prior conversation — cached subagent definitions from an earlier session will not see today's `.claude/agents/*.md` edits):

```
/goal Using docs/ADS_RowBuilder_Operating_Guide.md (v3.4) and docs/data-dictionary.xlsx
(including the Slug_Rules_Examples tab) as the authoritative references, populate
the next [N] currently-unstarted rows in data/ads_master.xlsx.

Preflight: confirm data/ads_master.xlsx, data/audit_log.xlsx, scripts/ads_xlsx_tool.py, and scripts/qa_verify_tool.py all exist - check via Read or Glob, never raw Bash (ls, test -f, inline Python). Glob silently omits files blocked by a permission deny rule instead of flagging the denial, so a Glob "not found" alone is not conclusive - confirm with Read first. A Read result of "denied by permission settings" means the file exists; only a genuine not-found from Read is a real miss. If a file is confirmed missing, stop immediately and report - do not create any of them. Also confirm Bash is rooted at the repo: run python scripts/ads_xlsx_tool.py --help via Bash - an error instead of usage text means Bash cwd is wrong. Stop immediately and report the error - never cd, never use an absolute path.

For each of the [N] target rows, in order:

1. Use the worker subagent, invoked fresh for this row - never more than one
row per invocation - to execute Batches 1-7 against the next currently-unstarted
row (via scripts/ads_xlsx_tool.py next-unstarted, never independent reasoning)
in Venue Number order, in data/ads_master.xlsx, per the three-state
blank-handling convention (real data / NOTFOUND - [reason] / N/A - [reason],
Venue Name Aliases excepted) exactly as in the Operating Guide. Worker must
never modify rows 2-7 (Venue Numbers 1-6). All reads/writes to
data/ads_master.xlsx, docs/data-dictionary.xlsx, and data/audit_log.xlsx go
through scripts/ads_xlsx_tool.py, in the exact shape in worker.md - no cd
prefix, no chaining, no redirects.

On NOTFOUND: the normal path is genuine research concluding the information
isn't available anywhere. Separately, if a fetch or search tool call fails
outright, treat that as a hard stop for that attempt, retry once if possible,
then continue via other sources - only mark NOTFOUND if nothing successfully
retrieved supports the field. Never fabricate detail to cover a failed fetch.

When worker hits a genuine fork it can't resolve from the Data Dictionary and
Operating Guide alone, invoke business-analyst. It may use web search to
ground its answer, and logs every resolution to data/audit_log.xlsx via
scripts/ads_xlsx_tool.py. Continue - never stop the loop to ask Troy.

If worker or business-analyst hits a genuine tool-capability gap - not a
research fork, the wrong or missing tool itself - stop and report immediately
rather than improvising a workaround.

2. Once worker completes this row, invoke qa immediately via
scripts/qa_verify_tool.py to verify: (1) all 70 fields resolved to one of the
three sanctioned states, and (2) rows 2-7 in data/ads_master.xlsx remain
byte-identical to the Answer Key.

3. If qa fails either check: HARD STOP the entire rung immediately, even if
not the last target row. Report exactly what completed, what failed, and why.

4. If qa passes: continue to the next target row and repeat from step 1.

DONE WHEN all [N] target rows complete this per-row worker-then-qa cycle, qa
confirming both conditions on every row.

STOP if 6 consecutive turns pass with no measurable progress, OR [turn_cap]
turns reached, OR [time_cap] minutes wall-clock elapse, OR an explicit
hard-stop named elsewhere in this goal (preflight missing-file/cwd, a
tool-capability gap, a qa failure) triggers - whichever first. An explicit
hard-stop is met immediately, not held pending the stall count - end the turn
and report the same turn it's identified. First three caps apply cumulatively
across all [N] rows, not per row. If stopped, report what completed, what
remains, and why.

On completion or stop, report: turns used, wall-clock time used, WebSearch
calls used, and all data/audit_log.xlsx entries generated this run - via
scripts/ads_xlsx_tool.py dump-audit-log, never inline Python.
```

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

**Endless/stuck loop** — qa never confirms DONE WHEN even though the row looks complete. *What to do:* check whether the Answer Key comparison is the culprit (did rows 2–7 actually drift?) versus a genuine gap in the target row's 70 fields. If it's a qa false-negative, that's a structural bug in `qa_verify_tool.py` worth fixing before continuing. As of v5, this failure mode should surface faster and more legibly at N>1 — a qa failure on row 2 of a 6-row rung now hard-stops immediately with a specific report, rather than only becoming visible (and ambiguous as to which row caused it) after all 6 rows had already run.

**Done-but-mediocre** — qa passes the row structurally but the content itself is thin or wrong. *What to do:* this is exactly what manual review (checklist item 2) is for — qa was deliberately built with no opinion on quality.

---

## 5. Reminders

**Before firing anything:**
1. Confirm all three subagent files (`worker.md`, `qa.md`, `business-analyst.md`) reflect the current fixed invocation shape, the tool-capability-gap stop-and-report rule, and — for worker/business-analyst — no reference anywhere to the Answer Key's existence or location.
2. Confirm `.claude/settings.json` matches the exact invocation shape both scripts require. A rule that matched an earlier, looser shape will silently stop protecting you.
3. Confirm `scripts/ads_xlsx_tool.py` and `scripts/qa_verify_tool.py` both exist and were tested manually at least once against the real Master and Answer Key before trusting them inside a live run.
4. Confirm the Answer Key genuinely lives at `C:/workspaces/non-repo/answer_key_ads_master.xlsx` and `data/answer_key_ads_master.xlsx` inside the repo no longer exists (a leftover copy in the old location would silently defeat the whole relocation).
5. Fire from a **fresh Claude Code session** — not a continuation of any session that predates today's subagent-file edits.
6. Archiving the Master between rungs is your job, not the loop's.
7. Loops are expensive in tokens, can thrash on a weak done-condition, and can "pass" while still being low quality. The caps and the sharp DONE WHEN above are what make this safe to run unattended — don't loosen either without a specific reason.
8. **New for v5:** this is the first rung where the per-row worker→qa loop actually does something different from a single-row rung — watch for it specifically. If the run behaves like Rung 1C (one long worker session, one qa check at the very end) rather than three distinct worker-then-qa cycles with a hard-stop gate between them, the `/goal` command text wasn't followed as written.
