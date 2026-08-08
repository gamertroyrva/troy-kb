# Restart Doc: ADS_MASTER Agentic Loop — Rung 1B Ready
*Supersedes `session-restart_ads-master-agentic-loop.md` (the Aug 6 "Rung 1 Launch-Ready" doc). That doc's "next physical action: fire Rung 1" is now stale — Rung 1 fired twice, and this doc picks up from where that left off. Do not use it as a status reference; this doc is current.*
*Companion: `goal-card_ads-master-agentic-loop_v3.md` is the technical spec — read both, don't treat this as a substitute for that.*

**Attach alongside this doc when resuming:** `about-me.md`, `anti-ai-writing-style.md`, `troy-voice-examples.md`, `spreadsheet-style-preferences.md`, `goal-card_ads-master-agentic-loop_v3.md`, `AgenticLoop_IssuesLog.xlsx`, `deferred-design-decisions_ads-agentic-loop.md`.

---

## 1. The Big Picture

Backlog Item 13: populate the currently-unstarted rows of Andrew's Driving Services' locations database (`data/ads_master.xlsx`, 70 fields per venue). The little-picture deliverable is the populated data. The big-picture purpose is Troy's own hands-on education in agentic loops as a technique — this project is the real-world guinea pig for that learning, not the point in itself.

## 2. You Are Here

**Rung 1 is done. Rung 1B (a single-row validation run, not a renumbering of the official rung sequence) is next.**

**Rung 1, attempt #1 (Aug 6):** Worker hit a genuine tool-capability gap — its subagent definition had no way to open or write a binary `.xlsx` file (no Bash). Claude Code began reasoning toward a workaround; Troy stopped it and asked for the setup itself to be fixed instead. Troy approved granting Bash to all three subagents. Claude Code made the edit correctly, but the retry still failed — subagent definitions are read once at session start, and a mid-session file edit isn't visible to a subagent spawned later in that same conversation. Troy ended the session rather than burn a third attempt against a known wall.

**Rung 1, attempt #2 (Aug 8, fresh session):** Fired clean. **DONE WHEN was met** — Venue Number 7 (Barboursville Vineyards, slug `barboursville`) fully populated across all 70 fields, qa confirmed both structural completeness and Answer Key integrity, manual verification checklist completed. This was a genuine, real milestone: the first fully autonomous three-agent row population, start to finish.

It was also noisy. A flood of permission prompts surfaced — some inherent to WebFetch/WebSearch being unpredictable and non-enumerable in advance (correctly left broad in settings.json, nothing to fix there), and some pointing at real, fixable infrastructure gaps. Troy Allow-Once'd through all of it to let the run finish, per the "attended runs use Allow Once, review afterward" policy established this session — then the postmortem (documented in full in `AgenticLoop_IssuesLog.xlsx`, Entries 8–10) surfaced three real issues, **all three now fixed and manually validated, but not yet tested inside a live `/goal` run:**

- **The `cd &&` invocation mismatch (Issue A / Entry 8):** worker's actual write commands used a compound shell form that didn't match settings.json's allow rule, so every write fell through to a prompt despite being safe and correct. Fixed by constraining worker's and business-analyst's invocation to one fixed shape and matching settings.json to it exactly.
- **qa's missing tool-script action (Issue B / Entry 9):** `ads_xlsx_tool.py` was built for worker's and business-analyst's jobs only; qa had no sanctioned way to run its two checks, so it fell back to raw inline Python — the exact wildcard pattern the tool script existed to eliminate. Fixed by building a second, separate script, `qa_verify_tool.py`, referenced only in `qa.md`.
- **Bash bypasses the Answer Key deny rule (Issue C / Entry 10):** settings.json's `Read`/`Edit` deny rules on the Answer Key path did nothing to stop a Bash-driven inline Python open of the same file — a real gap, not hypothetical, since qa demonstrated it live during the postmortem. Fixed by physically relocating the Answer Key outside the repo entirely, to `C:/workspaces/non-repo/answer_key_ads_master.xlsx`. Worker and business-analyst's subagent files no longer reference it at all — isolation now works by withholding the information, not by trying to block access to information they already had.

All three fixes were manually tested against the real repo after being built (`qa_verify_tool.py`'s two actions both returned clean passes against Venue Number 7's data and the relocated Answer Key). **None of the three has been exercised inside an actual autonomous `/goal` run yet.**

**Next physical action:** Troy fires the Rung 1B `/goal` command (§2 of Goal Card v3) in Claude Code, from a fresh session, after doing pre-run `/usage` record-keeping (session-0 and weekly_all, both readings — this is genuine Measurement Plan data, not optional).

## 3. Why Rung 1B Exists (and why it isn't "Rung 2")

Rung 1's own DONE WHEN was already met on attempt #2 — by the letter of the Goal Card, Rung 1 is complete. Rung 1B is not a repeat of that milestone. It exists specifically to answer one question the postmortem's manual testing couldn't: **do today's three fixes actually eliminate the permission-prompt flood in a live, autonomous run — not just when we call the scripts by hand?**

Rung 1B deliberately uses the *same* STOP-CAPS as Rung 1 (45 turns / 45 min) rather than advancing to Rung 2's 3-row scale. This is a control-variable choice: if turns-used and wall-clock come in meaningfully lower than attempt #2's, that's clean evidence the fixes worked. If Rung 1B were instead jumped straight to 3 rows, and something were still off, there'd be no way to cleanly tell whether the problem was "the fix didn't work" or "the fix worked but 3 rows revealed something new" — contaminated data, which is exactly what the Measurement Plan (§5 below) is trying to avoid.

If Rung 1B comes in clean — dramatically fewer interrupts, ideally near-zero for anything touching the two tool scripts — that's strong, cheap evidence to advance to Rung 2 (3 rows) with real confidence. If it's still noisy, better to have spent 1 row's budget finding that out than 3.

## 4. The Measurement Plan (why Rung 1, 1B, 2, 3 and 4 all matter beyond just "getting rows done")

No official or community-documented numbers exist for how many turns or how much of a Pro-plan 5-hour window a given row realistically costs — it genuinely depends on how much research each venue takes. Rung 1 attempt #2 gave a first real data point: **session-0 usage moved from 10% to 21% (an 11-point delta) for one row; weekly_all moved from 14% to 15% (1 point).** That number is a real ceiling, not a clean baseline — it includes real friction from the three issues just fixed (extra permission-prompt turns, qa's probing through multiple failed invocation attempts before landing on something that worked). Rung 1B's job is partly to produce a second, cleaner data point for comparison — did fixing the infrastructure noticeably reduce the session-0 delta for a comparable single row?

After Rung 1B, 2, 3 and 4, Troy records turns used, wall-clock time used, and a `/usage` reading before and after each. That data is what sets real, grounded STOP-CAPS for Rung 5 onward, rather than guessed numbers.

## 5. Small Things Worth Knowing

- Troy sometimes says **"wrong"** when he means **"rung"** — a voice-capture artifact, not a real word choice.
- "Relitigate" and "signal" are unwelcome words, per `anti-ai-writing-style.md`.
- **"Tuesday Morning Quarterbacking"** is the standing name for the recurring practice of reviewing what got Allow-Once'd or Allow-Always'd during an attended run and deciding what graduates into `.claude/settings.json`. Not a one-time session type — expect more of these as the loop scales.
- **The mystery of `settings.local.json` never appearing in `git status`** (Issues Log Entry 7) remains genuinely unsolved — project `.gitignore`, global `core.excludesfile`, and `.git/info/exclude` were all checked and none explain it. Not blocking anything; the file itself is now emptied out and superseded. Worth revisiting only if it becomes actually relevant again, not worth chasing for its own sake.
- **Deferred Decision #1** (worker/business-analyst granted Bash directly, rather than building a narrower purpose-built mechanism first) is still open, per `deferred-design-decisions_ads-agentic-loop.md`. Its trigger condition is a state change, not a row count: the first rung where Troy plans to be asleep or away, not present to Allow-Once anything. That rung hasn't been identified yet. Rung 1B and Rung 2 both remain attended.
- There's a live, unresolved tension named this session between "move fast, Allow-Always without reading closely" (Troy's habit from prior sessions, real speed benefit) and "know precisely what you granted" (needed for a settings.json that's actually designed, not archaeologically reconstructed). The resolved policy: **attended runs use Allow Once when uncertain, never Allow Always without understanding it; Tuesday Morning Quarterbacking afterward is where things graduate to permanent rules.** This does not yet solve the harder problem — an unattended run has no live decision point at all, meaning settings.json has to be complete enough that nothing novel is expected to come up, a materially higher bar than "good enough for attended," before that transition happens.

## 6. Reference Materials — Currency Check

- `goal-card_ads-master-agentic-loop_v3.md` — **current**, the technical spec. Supersedes v2 (see its own "What changed in v3" section for the full diff).
- `AgenticLoop_IssuesLog.xlsx` — current, 10 entries as of this doc, breadth-capture format (memory triggers, not full reasoning).
- `deferred-design-decisions_ads-agentic-loop.md` — current, depth-capture format for deliberate "good enough for now" tradeoffs. One open decision (#1) as of this doc.
- `agentic-loop-goal-designer-prompt_v2.md` — current, reusable template, no project-specific content to go stale.
- `ADS_RowBuilder_Operating_Guide.md` (v3.3) and `data-dictionary.xlsx` — current, live in `docs/`.
- `scripts/ads_xlsx_tool.py` and `scripts/qa_verify_tool.py` — current, live in `scripts/`, both manually tested against the real repo as of this doc. Neither has been exercised inside a live `/goal` run yet — that's Rung 1B's job.
- The three subagent files (`worker.md`, `qa.md`, `business-analyst.md`) — live in the repo itself, reflect all v3 changes. A future *chat* session doesn't need them re-uploaded, only a future *Claude Code* session touching them directly would.
- `goal-card_ads-master-agentic-loop_v2.md` and `v1` — superseded, prior-draft history only.
- `session-restart_ads-master-agentic-loop.md` (the Aug 6 restart doc) — superseded by this document.

## 7. Suggested Next Step on Resume

Nothing blocks Rung 1B from firing right now, independent of any future session — that's Troy's live action. Whenever a fresh session picks this up, the natural entry point is either (a) Rung 1B's actual results, ready for review, or (b) if Rung 1B hasn't fired yet, confirming the pre-fire checklist in Goal Card v3 §5 (subagent files reflect v3, settings.json matches the exact invocation shape, both scripts tested, Answer Key confirmed relocated, fresh session).
