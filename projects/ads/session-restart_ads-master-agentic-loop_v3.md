# Restart Doc: ADS_MASTER Agentic Loop — Rung 1C Ready
*Supersedes `session-restart_ads-master-agentic-loop_v2.md` (the Aug 8 "Rung 1B Ready" doc). That doc's "next physical action: fire Rung 1B" is now stale — Rung 1B fired, succeeded, and a full postmortem/revision cycle followed. Do not use it as a status reference; this doc is current.*
*Companion: `goal-card_ads-master-agentic-loop_v4.md` is the technical spec — read both, don't treat this as a substitute for that.*

**Attach alongside this doc when resuming:** `about-me.md`, `anti-ai-writing-style.md`, `troy-voice-examples.md`, `spreadsheet-style-preferences.md`, `goal-card_ads-master-agentic-loop_v4.md`, `AgenticLoop_IssuesLog.xlsx`, `deferred-design-decisions_ads-agentic-loop.md`, `ADS_RowBuilder_Operating_Guide.md` (v3.4), `data-dictionary.xlsx` (current), `settings.json` (current).

---

## 1. The Big Picture

Backlog Item 13: populate the currently-unstarted rows of Andrew's Driving Services' locations database (`data/ads_master.xlsx`, 70 fields per venue). The little-picture deliverable is the populated data. The big-picture purpose is Troy's own hands-on education in agentic loops as a technique — this project is the real-world guinea pig for that learning, not the point in itself.

## 2. You Are Here

**Rung 1B is done — DONE WHEN met.** Venue Number 8 (Blenheim Vineyards, slug `blenheim`) fully populated across all 70 fields, both qa checks passed (structural completeness + Answer Key integrity), fired clean from a fresh session with zero business-analyst escalations. This validated the three fixes v3 was built to test: the fixed invocation shape (no more `cd &&` mismatches), qa's dedicated script (`qa_verify_tool.py`, used exclusively, no raw-Python fallback), and the Answer Key relocation (no unauthorized diff or open occurred).

**It also surfaced six new issues, live, during the run — all six now addressed, none yet tested inside a live `/goal` run.** In order of what Troy cared about most:

1. **A second Answer Key isolation vector**, distinct from the one v3 already closed. While hunting for a way to identify the next unstarted row, worker *read* `qa_verify_tool.py`'s source (not executed it) — and that script's docstring/constants hard-code the Answer Key's relocated path. The relocation fix defeated the Bash-driven-open vector; it never anticipated "read the script that knows the path" as a separate vector. **Fixed two ways:** a new `Read(scripts/qa_verify_tool.py)` deny rule in `settings.json`, and — more fundamentally — worker now has a sanctioned way to find the next row and no longer has any reason to go looking near qa's script at all.
2. **No sanctioned mechanism existed for "find the next unstarted row" at all.** v3's own Goal Card said "worker determines the next unstarted row itself each time" and left it there — no method, just a definition. Worker filled that gap with raw inline Python (the direct cause of issue #1 above). **Fixed:** new `next-unstarted` action added to `ads_xlsx_tool.py`, paired with a new explicit CONSTRAINT — the Master fills strictly in ascending Venue Number order, top to bottom, no skipping, no going back, ever. That ordering guarantee is what makes the check's underlying logic (first row where `Last Verified/Updated` is blank) safe rather than merely lucky, and it self-corrects cleanly if qa ever fails and worker gets re-engaged — the same row just comes back up, since a qa failure means that field was never written.
3. **WebSearch cannot be auto-approved via `permissions.allow` in current Claude Code at all** — confirmed via search as an open, unshipped feature request, not a settings.json misconfiguration. WebFetch technically can be, but its `domain:*` wildcard is separately documented as buggy, and `settings.json` had zero allow entries for either tool anyway. This was the single largest source of prompt volume during Rung 1B, and it directly threatens the "runs while Troy is asleep" goal — no config change can fix it. **Fixed:** a `PreToolUse` hook in `settings.json` (`matcher: "WebSearch|WebFetch"`) that returns an explicit allow decision, bypassing the broken/missing config path entirely. Safe because both tools are read-only; Bash remains separately and narrowly scoped, so nothing fetched can pivot into file mutation.
4. **A second, unrelated Claude Code bug was independently defeating the sanctioned-script Bash allow rule.** Confirmed via search: the `*` wildcard in Bash allow rules fails to match commands containing double-quote characters — and nearly every `write-field` call quotes its `--field`/`--value` arguments, which is exactly the pattern that breaks it. This explains most of the second-half prompt flood far more completely than any single field-level issue could; it's a separate root cause from the `cd &&` shape mismatch v3 already fixed. **Fixed:** a second `PreToolUse` hook (`matcher: "Bash"`) that inspects the literal command string and allows only when it genuinely starts with one of the two sanctioned scripts — narrower than the WebSearch/WebFetch hook by design, so it reinforces rather than widens the "Bash is safe because narrowly exercised" premise behind Deferred Decision #1.
5. **A literal `$` in a price-field value required escaping and still triggered a prompt.** Root cause is Bash variable-expansion syntax, one layer before Python or Excel ever see the value — so no amount of spreadsheet-side typing fixes it. **Fixed:** new Price Field Convention — price values write as plain numbers, no currency symbol (`25/person`, not `$25/person`). Documented in Operating Guide v3.4 and flagged in the Data Dictionary's Instructions column for Fields 39, 42–45. Data Dictionary side of this — done, Troy handled it directly.
6. **Wall-clock hit ~52 minutes against a 45-minute STOP-CAP and the run didn't stop.** Noted, deliberately not investigated — Troy's explicit call, filed as an interesting observation for whenever STOP-CAPS matter more (larger row counts, unattended runs), not urgent now.

**One item was raised and deliberately closed without a fix:** worker skipped the "stop and report" rule when it hit the next-unstarted gap (issue #2), instead of stopping per v3's explicit CONSTRAINT. Troy's call: this rule was the least important thing introduced in the prior revision cycle, its value is genuinely untested, and it's likely harmless either way — not worth further investment this cycle, especially now that its one observed trigger is closed by the `next-unstarted` fix.

**Next physical action:** Troy fires Rung 1C's `/goal` command (Goal Card v4, §2) in Claude Code, from a fresh session, after pre-run `/usage` record-keeping (session-0 and weekly_all, both readings).

## 3. Why Rung 1C Exists (and what it's specifically testing)

Rung 1C is not a repeat of Rung 1B's milestone — that DONE WHEN was already met. It exists to answer two things the manual work this session couldn't:

- **Do the two new `PreToolUse` hooks actually suppress the prompt volume they were built to suppress?** Neither has been exercised live. If Rung 1C comes back dramatically quieter — especially on WebSearch/WebFetch and on quoted `write-field` calls — that's strong evidence the hooks work and a real step toward the actual goal (an unattended run). If it's still noisy, the two hooks are cleanly separable, so it'll be immediately obvious which one isn't pulling weight.
- **Does the `next-unstarted` action + ordering rule work under live autonomous conditions**, the same way v3's fixes got validated by Rung 1B before being trusted further? This closes the loop on the Answer-Key-path-leak vector (issue #1 above) as well as the improvised-Python problem (issue #2) — if worker never has a reason to look elsewhere, both symptoms should simply not recur.

Same STOP-CAPS discipline as before applies: keep the row count where it's cheapest to get a clean read before scaling to Rung 2's 3 rows.

## 4. The Measurement Plan

Data so far:
- Rung 1 attempt #2 (1 row, pre-fixes): session-0 delta 11 points, weekly_all delta 1 point.
- Rung 1B (1 row, v3 fixes applied, zero business-analyst escalations): session-0 delta **17 points**, weekly_all delta **2 points** — higher on both, not lower. Worth noting honestly: this doesn't cleanly confirm the fixes reduced overhead, but it's also not a clean apples-to-apples comparison — Rung 1B ran longer wall-clock (~52 min vs. cap) and had zero business-analyst escalations, so the two runs may not be doing equivalent work. Two data points is a thin trend either way.

Rung 1C is the next chance for a cleaner read, especially isolating whether the hooks specifically move the needle on turns/time, since they target the actual friction source (prompt volume) more directly than v3's fixes did.

Record before/after `/usage` (session-0 and weekly_all) for Rung 1C same as prior rungs — non-optional.

## 5. Small Things Worth Knowing

- Troy sometimes says **"wrong"** when he means **"rung"** — a voice-capture artifact, not a real word choice.
- "Relitigate" and "signal" are unwelcome words, per `anti-ai-writing-style.md`.
- **"Tuesday Morning Quarterbacking"** remains the standing name for reviewing what got Allow-Once'd or Allow-Always'd during an attended run and deciding what graduates into `.claude/settings.json`.
- Deferred Decision #1 (worker/business-analyst granted Bash directly) is still open — trigger is the attended → unattended state change, not a row count. Not yet identified which rung that is. The new Bash `PreToolUse` hook reinforces rather than resolves this decision — it narrows what Bash calls get auto-approved, but Bash access itself remains as broad as Decision #1 originally granted.
- The `settings.local.json` mystery from Rung 1 attempt #1 (Issues Log Entry 7) remains genuinely unsolved and is not worth chasing further — file is emptied out and superseded.
- This session generated a full revision cycle (Issues Log Entries 11–16) using a **priority-ordered format**, not chronological — worth knowing if referencing "the issues from Rung 1B" that the log's own row order and the discussion order Troy actually worked through don't match; the log stays chronological by Entry #, the working session was reordered by Troy's stated priority.

## 6. Reference Materials — Currency Check

- `goal-card_ads-master-agentic-loop_v4.md` — **current**, the technical spec. Supersedes v3 (see its own "What changed in v4" section for the full diff; v3's changelog is preserved below it for history).
- `AgenticLoop_IssuesLog.xlsx` — current, 16 entries as of this doc (10 from before this session, 6 new from Rung 1B's postmortem).
- `ADS_RowBuilder_Operating_Guide.md` — **v3.4**, current. New sections: Price Field Convention, Row Ordering & Next-Unstarted Convention.
- `data-dictionary.xlsx` — current, Troy updated the Instructions column for Fields 39, 42–45 (Price Field Convention pointer) directly.
- `.claude/settings.json` — current, three changes from the version Rung 1B ran against: new `Read(scripts/qa_verify_tool.py)` deny rule, new `hooks.PreToolUse` block with two matcher entries (WebSearch/WebFetch, Bash) — both **untested live**, that's Rung 1C's job.
- `scripts/ads_xlsx_tool.py` — current, new `next-unstarted` action added and syntax-verified. Not yet exercised inside a live `/goal` run.
- `scripts/qa_verify_tool.py` — unchanged this cycle.
- `deferred-design-decisions_ads-agentic-loop.md` — current, still one open decision (#1), unchanged this cycle.
- The three subagent files (`worker.md`, `qa.md`, `business-analyst.md`) — live in the repo. A future *chat* session doesn't need them re-uploaded unless a future *Claude Code* session is touching them directly.
- `session-restart_ads-master-agentic-loop_v2.md` (the Aug 8 "Rung 1B Ready" doc) — superseded by this document.

## 7. Suggested Next Step on Resume

Nothing blocks Rung 1C from firing right now, independent of any future session — that's Troy's live action. Whenever a fresh session picks this up, the natural entry point is either (a) Rung 1C's actual results, ready for review — with particular attention to whether the two hooks visibly cut prompt volume — or (b) if Rung 1C hasn't fired yet, confirming the pre-fire checklist in Goal Card v4 §5, plus the subagent-file sync check noted in §6 above, which is new to this version and wasn't a concern before today's changes.
