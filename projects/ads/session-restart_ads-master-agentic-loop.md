# Restart Doc: ADS_MASTER Agentic Loop — Rung 1 Launch-Ready
*Supersedes `goal-card-session-restart_ads-master-agentic-loop.md` — that doc's architecture description (scratch-copy concept, §1/§5) is now stale; do not use it as a reference. This doc is the current source of session history and status.*
*This doc is narrative/status. `goal-card_ads-master-agentic-loop_v2.md` is the technical spec — read both, don't treat this as a substitute for that.*

**Attach alongside this doc when resuming:** `about-me.md`, `anti-ai-writing-style.md`, `troy-voice-examples.md`, `spreadsheet-style-preferences.md`, `goal-card_ads-master-agentic-loop_v2.md`, `agentic-loop-goal-designer-prompt_v2.md`.

---

## 1. The Big Picture

Backlog Item 13: populate the 190 currently-unstarted rows of Andrew's Driving Services' locations database (`data/ads_master.xlsx`, 70 fields per venue). The little-picture deliverable is the populated data. The big-picture purpose is Troy's own hands-on education in agentic loops as a technique — this project is the real-world guinea pig for that learning, not the point in itself.

## 2. The Plan

A three-agent architecture running inside Claude Code's `/goal` command:
- **worker** (Sonnet 5, Medium) — executes RowBuilder's existing 7-batch research-and-population process against unstarted rows, one row fully completed before the next begins.
- **qa** (Sonnet 5, Low) — narrow, binary, machine-checkable gate: structural completeness of newly-populated rows, plus a byte-identical integrity check of the 6 pre-completed rows against the Answer Key. No opinion on content quality — that stays entirely manual, Troy's call.
- **business-analyst** (Opus 5, High) — resolves worker's genuine judgment-call forks (ambiguous sourcing, categorization, ordering not covered by documented rules), grounded in web research where useful. Never stalls the loop, never pages Troy directly. Logs every resolution to a 9-column audit log.

Supervision is a **graduated rope**, not live interruption: 1 → 3 → 6 → 10 → 25 → 50 → 100 → 190 rows, each a separate `/goal` firing, Troy reviewing real output (including the audit log) between invocations before granting the next rung.

**The Goal Card is the source of truth; the `/goal` command is generated from it, not the other way around.** When something needs to change, revise the Goal Card first, then regenerate the command. This is how today's Revision Cycle actually worked, and it should keep working that way.

**Architecture note worth carrying forward, not just the conclusion:** the loop was originally designed around an in-loop scratch copy of the Master, with Troy manually merging finished work back afterward. That design was abandoned mid-session — not because it was unsafe, but because it was solving a problem (recoverability) that Troy's own external archiving already solves better, while also leaving an unspecified merge-back step nobody had actually designed. The loop now edits `data/ads_master.xlsx` directly. Recoverability comes from two independent places: Troy manually archiving an interim copy of the Master after each rung he approves (his responsibility, outside the loop entirely), and git itself, since the Master and Answer Key are already tracked with clean history. If a future session encounters any document still describing a scratch copy, that document is stale — this is the current design.

**A second thing worth carrying forward:** the Answer Key isolation (worker and business-analyst must never access it) is enforced at the *prompt* level in the three subagent files, not via any hard filesystem access control — Claude Code doesn't appear to support per-file permissions, only tool-level and directory-level scoping. This was a conscious, discussed tradeoff, not an oversight. If it ever needs to become a hard guarantee, the move would be physically relocating the Answer Key outside the repo directory entirely.

## 3. You Are Here

**Rung 1 is cleared for launch.** Specifically, as of this session:
- **[RESOLVED]** Goal Card v2 exists, incorporates a full 9-item Revision Cycle, and Troy has read it in full via Obsidian.
- **[RESOLVED]** All three subagent files (`worker.md`, `qa.md`, `business-analyst.md`) are written against Goal Card v2 and placed at `.claude/agents/` in the `andrews-driving-services` repo.
- **[RESOLVED]** `data/audit_log.xlsx` — 9 columns (Entry #, Rung, Venue Number, _slug, Batch, Field, Question, Resolution, Timestamp), headers only, styled per Troy's spreadsheet preferences — is built and placed in the repo.
- **[RESOLVED]** `.gitignore` updated: `~$*` (Office lock files) and `data/audit_log.xlsx` (path-scoped) added. The Master and Answer Key remain tracked in git, deliberately.
- **[RESOLVED]** Byte-identical check between `data/ads_master.xlsx` (rows 2–7) and `data/answer_key_ads_master.xlsx` — run against files freshly re-uploaded directly from the repo, not an earlier session snapshot — **passed, zero mismatches.** Venue Numbers 1–6 confirmed in correct sequence; row 8 confirmed as the correct starting point (Venue Number 7, slug `barboursville`).

**Next physical action:** Troy fires the rung 1 `/goal` command (§2 of Goal Card v2) in Claude Code, after doing pre-run `/usage` record-keeping (see §4 — command name corrected from an earlier `/status` reference).

## 4. Open Items — Queued, Not Yet Applied

Both of these are explicitly deferred to the rung 1 → rung 2 prep session, not urgent for rung 1 itself:

1. **Row-sequencing statement missing from the Goal Card.** The subagent files already say "complete one row fully before starting the next" (added this session, in worker.md) — but Goal Card v2's STAGES section and the `/goal` command text itself never state this explicitly. Worth fixing before any multi-row rung fires, since it affects what a capped-out run leaves behind (clean row boundaries vs. multiple simultaneously half-done rows).
2. **`/status` → `/usage` correction.** Goal Card v2's Reminders and Verification Checklist both currently reference `/status` for pre/post-rung usage tracking. `/status` wasn't available in Troy's environment; `/usage` is the correct command for token-consumption/rate-limit tracking and is actually the better fit for the original intent anyway.

## 5. The Measurement Plan (why rungs 1–3 matter beyond just "getting rows done")

No official or community-documented numbers exist for how many turns or how much of a Pro-plan 5-hour window a given row realistically costs — it genuinely depends on how much research each venue takes. So rungs 1, 3, and 6 aren't just early progress — they're the actual data-gathering instrument. After each, Troy records turns used, wall-clock time used, and a `/usage` reading before and after. That data is what sets real, grounded STOP-CAPS for rungs 4 (10 rows) onward, rather than guessed numbers. This should stay in mind through at least the first three rungs — don't skip the `/usage` before/after habit even if it feels redundant on a fast, clean run.

## 6. Small Things Worth Knowing

- Troy sometimes says **"wrong"** when he means **"rung"** — a voice-capture artifact, not a real word choice. If a message says "wrong 3," it almost certainly means rung 3.
- "Relitigate" is now an unwelcome word, same standing as "signal" in `anti-ai-writing-style.md`.

## 7. Reference Materials — Currency Check

- `goal-card_ads-master-agentic-loop_v2.md` — **current**, the technical spec.
- `agentic-loop-goal-designer-prompt_v2.md` — current, reusable template, no project-specific content to go stale.
- `ADS_RowBuilder_Operating_Guide.md` (v3.3) and `data-dictionary.xlsx` — current, live in `docs/`.
- The three subagent files — live in the repo itself now; a future *chat* session doesn't need them re-uploaded, only a future *Claude Code* session touching them directly would.
- `goal-card_ads-master-agentic-loop.md` (**v1**, no version suffix) — superseded, prior-draft history only.
- `goal-card-session-restart_ads-master-agentic-loop.md` (the original restart doc) — superseded by this document; its architecture section is stale (see §2 above).

## 8. Suggested Next Step on Resume

Nothing blocks rung 1 from firing right now, independent of any future session — that's Troy's live action, not something requiring more Claude Chat work first. Whenever a fresh session picks this up, the natural entry point is either (a) rung 1's actual results, ready for review, or (b) the rung 1 → 2 prep work in §4, if rung 1 hasn't fired yet.
