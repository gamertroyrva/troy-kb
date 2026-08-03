# Restart Doc: Goal Card Design — ADS_MASTER Agentic Loop
*Session paused before interview completion. This doc lets a future session resume without re-deriving the architecture below.*

**Purpose of this session:** Design a `/goal` Goal Card (per `agentic-loop-goal-designer-prompt_v2.md`) using an interview process. The little-picture deliverable is the Goal Card itself. The big-picture purpose is learning agentic loops as a technique — Backlog Item 13 (ADS_MASTER build-out) is the real-world guinea pig, not the point in itself.

**Attach alongside this doc when resuming:** `about-me.md`, `anti-ai-writing-style.md`, `troy-voice-examples.md`, `agentic-loop-goal-designer-prompt_v2.md`, current `data-dictionary.xlsx`, current `ADS_RowBuilder_Operating_Guide.md`, current `ads_master.xlsx`.

---

## 1. The Big Architectural Decision: Three Autonomous Agents

Original template only anticipated two roles (worker + evaluator). This session concluded three are needed, and that the loop should run **fully autonomously — no mid-run interruption of Troy.**

| Name (lowercase, hyphenated) | Modeled on Troy's career role | What it does | When it fires |
|---|---|---|---|
| **worker** | Software developer | Executes RowBuilder's existing batch-by-batch research and population process (Batches 1–7, fields per the Operating Guide) against currently-unstarted rows. | Continuously, per batch, per row. |
| **qa** | QA specialist | Narrow, binary, machine-checkable pass/fail gate. Confirms N of the currently-unstarted rows are fully resolved (every field in one of the three sanctioned states) and that the 6 pre-completed rows remain byte-identical to Answer Key. Has no opinion on content quality — only structural/procedural completeness. This is what lets `/goal` decide to stop. | At the loop's native condition-check cadence. |
| **business-analyst** | Business systems analyst (Troy's actual job title) | Stands in for Troy on judgment calls the worker can't resolve from the Data Dictionary + Operating Guide alone (ambiguous sourcing, categorization calls, ordering not covered by documented rules, etc.). Resolves and returns an answer — never stops the loop, never pages Troy. | On-demand, whenever worker hits a genuine fork, at any point within any batch. |

**Key reasoning that got us here:** This is low-stakes, fully reversible spreadsheet work — not a domain requiring live human-in-the-loop safety supervision ("not NASA"). An agent that pings Troy constantly to stay running isn't autonomous, it's outsourcing intelligence back to him. Real supervision should happen at Troy's chosen cadence, not the loop's.

---

## 2. The Supervision Mechanism: Graduated Rope, Not Live Interruption

Troy does **not** want to be interrupted mid-run. Instead:

- The loop is invoked in escalating batch sizes: **1 row → 3 → 6 → 10 → 25 → 50 → 100 → 196 (well, 190 — see DONE WHEN below)**
- Each invocation is a separate Goal Card firing, using one shared template with row-count (and scaled STOP-CAPS) as the parameter that changes
- Troy reviews real output — including the audit log (see §4) — between invocations, and only then decides whether to grant the next rung of rope
- This is the actual review/course-correct mechanism that batching originally existed to provide in RowBuilder's interactive-mode days — just relocated from "live, unpredictable" to "fixed, Troy-controlled checkpoints"

---

## 3. DONE WHEN (row-completion definition)

> **N of the 190 currently-unstarted rows are now fully populated across all 70 fields** (every cell resolved to one of the three sanctioned states: real data, `NOTFOUND — [reason]`, or `N/A — [reason]`; Venue Name Aliases is the sole exception where true-blank is correct), **AND the 6 pre-completed rows remain byte-identical to Answer Key.**

Row count math, confirmed against the actual uploaded `ads_master.xlsx`:
- 196 total data rows (header in row 1, data rows 2–197)
- **6** pre-completed rows (rows 2–7 in the file as uploaded — not 10, that was an earlier hypothetical)
- **190** currently-unstarted rows (row 8 onward) — some of these already carry a few stray non-identity fields from prior processes (e.g. ListMaker), but "unstarted" means *not yet run through RowBuilder*, not *literally blank*. This is settled, not an open item.

Terminology note (locked in): use **"unstarted,"** not "blank" — unstarted rows already carry identifying fields (slug, venue name, venue type, sometimes partial address) from ListMaker; they are not empty.

---

## 4. Audit Log Spec

Separate file: **`audit_log.xlsx`** (mirrors the Answer Key precedent — separation of concerns, keeps ADS_MASTER as the clean artifact).

One row per notable business-analyst resolution. Columns:
1. **Row identifier** (slug — and pending the numerical ID addition below, that ID too)
2. **Question** — what worker asked
3. **Resolution** — what business-analyst decided

Purpose: gives Troy a way to audit the *reasoning*, not just the output, at each rope checkpoint — a way to catch bad judgment early (e.g. at row 1 or row 3) rather than discovering it at data row 196.


---

## 5. Reference Materials Now in Context (as of this session)

- **Data Dictionary  — 70 fields across 7 batches. Includes Slug_Rules_Examples tab (construction rules + worked examples) and Version tab.
- **ADS RowBuilder Operating Guide  — defines: 7-batch structure, all batches run on **Sonnet 5, Medium effort**; three-state blank-handling convention (Blank/NOTFOUND/N/A, with required brief reason for the latter two); Source References pipe-delimiter transcription convention; Empty Fetch Discipline (never fabricate on failed fetch — hard stop, retry once, else NOTFOUND); timestamp convention (nearest hour, ET, both fields identical).
- **`ads_master.xlsx`** — confirmed structure: 70 columns matching the Data Dictionary exactly, header row 1, rows 2–7 complete (6 rows), rows 8–197 unstarted.
- **ads_master_answer_key.xlsx** — not yet uploaded. Concept settled: separate document, holds the 6 known-good rows, used by qa as an integrity check (not a grading rubric for new rows — there is no ground truth for those).

---

## 6. Interview Status (per `agentic-loop-goal-designer-prompt_v2.md`)

**Answered / settled:**
- ✅ Objective (build ADS_MASTER's 190 unstarted rows via the worker/qa/business-analyst architecture)
- ✅ Done-when at row/batch level (§3 above)
- ✅ Whether anything writes to live/production systems — no; reversible spreadsheet work

**Asked but not yet answered:**
- ⬜ Scratch/output path — does the loop write directly into `ads_master.xlsx`, or a scratch copy Troy merges back manually? (Question 2 — paused mid-answer when the batching discussion opened up)

**Not yet asked:**
- ⬜ Quality bar specifics beyond "follow the Data Dictionary and Operating Guide" — anything to add?
- ⬜ Constraints / off-limits, beyond "don't touch production" (not obviously applicable here, but worth asking explicitly per template)
- ⬜ Prior-attempt history — has anything like this been tried before, what went wrong (if anything)?
- ⬜ **worker** model/tier — Operating Guide already specifies Sonnet 5 Medium for RowBuilder; does that carry over as-is for the loop's worker, or does loop context change the answer?
- ⬜ **qa** model/tier — not yet discussed
- ⬜ **business-analyst** model/tier — not yet discussed (this role didn't exist in the original two-agent template, so this is a new question the template didn't anticipate)
- ⬜ Turn/runtime cap — the graduated-rope *sizes* are settled, but the actual STOP-CAPS numbers (max turns or runtime per rung) are not

**Note for whoever resumes:** the original template's Step 3 push-back check ("push back hard if...") should still be run once, formally, after the full interview is complete — it hasn't been run yet, since the interview itself isn't finished.

---

## 7. Suggested Next Step on Resume

Pick up at the scratch/output path question (Question 2), then continue straight through the remaining unanswered items in §7. The architecture (§1–§4) is stable enough to treat as settled input, not something to re-litigate, unless new information surfaces. `agentic-loop-goal-designer-prompt_v2.md` will put a limit on the number of interview questions that can be asked--take that with a grain of salt--getting the goal card in a ready state with genuinely high probability of positive outcomes when used takes priority over an arbitrary interview questions limit.
