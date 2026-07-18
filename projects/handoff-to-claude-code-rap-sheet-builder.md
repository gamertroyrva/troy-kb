# Handoff to Claude Code: Richmond's Most Unwanted — The Rap Sheet Builder

*Prepared in Claude Chat, July 2026, after rule-testing against 3 of 6 (soon 7) historical trades.*
*Goal of this Code task: turn the rules below into a repeatable Python tool. No new rules should need to be invented here — if something doesn't fit, stop and flag it rather than guessing.*

---

## What This Project Is

Troy organizes local no-ship math trades for the Richmond, VA board game community (project: RVA Math Trade). Every trade produces a GeekList export with two sections: **MOST WANTED** (ranked, one line per listing) and **UNWANTED** (alphabetical, one line per listing). "Unwanted" means zero people put that item on a want list.

**The Rap Sheet** is a master table — one row per game title, one column per trade — showing whether that title was Wanted, Unwanted, or didn't appear, in each trade. It's the foundation for an eventual "Richmond's Most Unwanted" analysis (tiered lists of repeat offenders), but building those tiers is **out of scope for this Code task** — that requires human judgment against the actual assembled data and will happen in a follow-up Claude Chat session, then a *second* Code handoff. This task's job is: raw files in, Rap Sheet out. Nothing fancier.

---

## Input Files

Six (soon seven) plain-text GeekList exports, one per trade:

| Trade Name | ID |
|---|---|
| Richmond VA 2023 March No-Ship | 2023_03_MT |
| Richmond VA 2024 January No-Ship | 2024_01_MT |
| Richmond VA 2024 August No-Ship | 2024_08_MT |
| Richmond VA 2025 January No-Ship | 2025_01_MT |
| Richmond VA 2025 September No-Ship | 2025_09_MT |
| Richmond VA 2026 January No-Ship | 2026_01_MT |
| *(Summer 2026 trade — not yet run, will become 2026_0X_MT)* | *pending* |

**Input strategy — full rebuild, every time (decided, not open for reinterpretation):** When a new trade becomes available (e.g., Summer 2026), the tool takes **all N raw Most Wanted files** as input — not the previous Rap Sheet plus one new file. The Rap Sheet is always regenerated from scratch from the complete set of raw source files. This is a deliberate choice: raw files are trivial to archive/relocate, compute cost is Troy's own laptop and negligible for files this size, and "always a pure, reproducible function of the raw inputs" beats "faster but stateful and capable of drifting" if the tool's logic is ever patched later. Do not design any incremental-append or "diff against last run" logic — it is explicitly not wanted.

Each file has this structure:

```
[header/preamble lines]
MOST WANTED
<rank>/<total_wants>/<something> - <Title> (<listing_id>) [<owner>]
<rank>/<total_wants>/<something> - <Title> (<listing_id>) [<owner>]
...
UNWANTED (<declared_count>)
<Title> (<listing_id>) [<owner>] - <number>
<Title> (<listing_id>) [<owner>] - <number>
...
```

**Known real-world messiness to expect and handle gracefully, not silently:**
- One historical file (2024_01) was found truncated mid-upload (fewer lines than the declared `UNWANTED (N)` count implied). **Always validate parsed line count against the declared count in the `UNWANTED (N)` header** and flag a loud warning if they don't match — don't just proceed with partial data.
- One trade (2024_08) had a single malformed line with no title and a placeholder ID: `*** 5/133/ - (0) []`. Any line that fails to parse should be collected and reported, not silently dropped without visibility. **Malformed/unparseable lines ARE excluded from the Total Unique Titles count and from Wanted/Unwanted counts (they have no title to attach a status to) — but they ARE still counted toward "GeekList Items in the trade."** That top-line number is meant to match an externally-known total, and a malformed line was still a genuine listing in the export, so it belongs there. Do not exclude it from that count.
- Line endings may be `\r\n`. Strip appropriately.

---

## The Rules (already tested, do not re-derive)

### Scope exclusions
- **"Alt Name:" entries** (cash/PayPal/Venmo placeholders, non-cataloged upgrade/accessory listings) are excluded entirely from every count, in both sections. These are not real BGG-tracked games.
- Any line that fails to parse into a valid title/id/owner structure is excluded from Total Unique Titles and Wanted/Unwanted counts (no title = no status), but IS still counted toward "GeekList Items in the trade" — and must be logged/reported regardless.

### Matching
- Game identity = **exact title text as printed**, case-sensitive-or-not is a judgment call for whoever builds this (Troy hasn't specified; recommend exact match including case, and log any near-duplicate titles that differ only by case or whitespace so Troy can eyeball them — do not auto-merge).
- No fuzzy matching, no collapsing of expansions/variants into base games. "Munchkin" ≠ "Munchkin Disney". Two different expansions of the same base game are different titles. Different copies/owners of the *identical* title text ARE the same game.

### Per-trade status (Rule 1)
- **If a title appears anywhere in MOST WANTED for a trade, its status for that trade is WANTED** — even if the same title also appears one or more times in UNWANTED for that same trade.
- Otherwise, if the title appears in UNWANTED, its status is UNWANTED.
- This makes per-trade status strictly binary: WANTED or UNWANTED. This binary collapse (Rule 1) is a **per-trade-only** simplification — see "The Rap Sheet" section below for why it does NOT carry into the cross-trade output.

### Per-trade QA metrics (reference numbers Troy will reconcile against; get these right)
For each trade, compute and report:
1. **GeekList Items in the trade** = total raw listing lines across MOST WANTED + UNWANTED (including Alt Name entries, including duplicate titles as separate lines, excluding only truly unparseable lines). This number should match the trade's own externally-known total when Troy has one — surface it prominently so he can check.
2. **Total unique game titles** = GeekList Items, minus Alt Name entries, deduped by exact title.
3. **Wanted** = count of unique titles with status WANTED.
4. **Unwanted** = count of unique titles with status UNWANTED (post-Rule-1).
5. Sanity check: Wanted + Unwanted must equal Total unique game titles. Assert this; fail loudly if it doesn't hold.
6. Also report: which titles were "overridden" by Rule 1 (appeared in both sections) — useful signal, not just a discarded detail.

---

## The Rap Sheet (the actual deliverable)

This is the cross-trade output. **Rule 1's binary collapse does NOT apply here** — this table is the raw, granular truth precisely because collapsing it would erase the story (a title going from Unwanted to Wanted over time, etc.).

### Analytical Universe
The set of titles that were **UNWANTED (post-Rule-1) in at least one trade.** Titles that were WANTED every single time they appeared, across every trade, and never once UNWANTED, are **excluded from the Rap Sheet entirely** — they're not part of this story.

### Three-state flag, per title, per trade
For every title in the Analytical Universe, compute its status in **every** trade (including trades it wasn't part of):

- **WANTED** — appeared in MOST WANTED that trade (regardless of also appearing in UNWANTED — Rule 1 still applies *within* a single trade's status, just not across trades)
- **UNWANTED** — appeared in UNWANTED that trade and not in MOST WANTED
- **NOT_LISTED** — title did not appear in either section of that trade's GeekList at all

### Output shape

This is The Rap Sheet. One row per title, one column per trade, alphabetized by title (no ranking/tiering — that's the follow-up phase). Example:

| Title | 2023_03_MT | 2024_01_MT | 2024_08_MT | 2025_01_MT | 2025_09_MT | 2026_01_MT |
|---|---|---|---|---|---|---|
| A Song of Ice & Fire: Tabletop Miniatures Game – Stark vs Lannister Starter Set | UNWANTED | UNWANTED | NOT_LISTED | ... | ... | ... |
| Axis & Allies: 1941 | NOT_LISTED | UNWANTED | UNWANTED | ... | ... | ... |

**Deliverable format — xlsx, written locally (decided):** The Rap Sheet is delivered as an `.xlsx` file, written to Troy's local machine (same environment the raw input files live in — input and output stay together, no cloud round-trip). Use `openpyxl` (or equivalent) rather than the Google Sheets API — input files are local, output should be too, and a local xlsx write is more deterministic and dependency-free (no auth, no network, no rate limits) than round-tripping through Sheets. Nothing prevents Troy from manually dragging the finished file into Drive later if he wants to view/share it there — that's a post-hoc choice, not something the tool needs to handle.

Since this bypasses the Sheets API, Troy's `spreadsheet-style-preferences.md` formatting rules do **not** come for free and must be explicitly applied via `openpyxl`: left-justified text (center-justified for short numeral/code columns, though none are expected in the Rap Sheet itself), bold black text on a mid-tone gray background for the frozen header row, gridlines throughout, one trailing genuinely-empty gridlined row, automatic row height, no column freezing beyond the header row, consistent professional font (Arial baseline). Presentation-layer treatment of NOT_LISTED as a blank cell instead of literal text is a nice-to-have worth considering, not a requirement — flag it as an easy option rather than building it by default.

---

## Orchestration — How This Actually Runs (decided: Option B, batch-all)

This section exists because the rest of the document describes *what* gets computed and *what* the final artifact looks like, but not *when* human review happens — that ambiguity needs to be closed before Code builds anything, or a workflow will get invented by default.

**Decided approach: batch-all, review-after.** The tool ingests **all N raw trade files in a single run** and produces, in one pass:
1. Per-trade QA metrics for every trade (GeekList Items, Total Unique Titles, Wanted, Unwanted, the Rule 1 overridden-titles list, and any parsing anomalies/warnings) — for **all trades**, presented together.
2. The Rap Sheet — generated last, after all per-trade processing is complete.

**Explicitly not wanted:** a design where the script processes one trade, halts, and waits for Troy to bless that trade's numbers before moving to the next file. That gated/sequential pattern belongs to a different philosophy (protecting against a bad trade contaminating downstream state) that doesn't apply here — Option 2 above already establishes that the whole pipeline is a full, cheap rebuild from raw files every time, so there's no accumulated state to protect mid-run.

**What this means for output structure:** present the per-trade QA summaries for all trades first (in trade-chronological order), so Troy's eyes can do the same left-to-right sanity pass across all trades that he did manually in Chat — but do this as one continuous report from one run, not as N separate gated steps. The Rap Sheet comes after, as the final section/output of that same run. Troy reviews everything — QA summaries and Rap Sheet alike — after the full batch completes, and if something looks wrong in any trade, the fix is: correct the source file, rerun the whole batch (Option 2 rebuild logic makes this cheap and correct-by-construction).

---

## Repo Placement (decided)

This tool lives in the **existing `math-trade-genie` repo**, alongside the numbered blocks (`00-trade-counter/`, `01-loop-parser/`, `02-html-visualizer/`, `03-anomaly-detector/`) — but it is **not** one of the numbered blocks, and should not be given a number (e.g., not `04-rap-sheet-builder/`).

Reasoning, so this doesn't get "corrected" back to a numbered block later: Blocks 0–3 form a genuine pipeline — they all key off a **results/wants file** (the artifact produced once an OLWLG run, practice or final, completes and settles into trades), and Blocks 1–3 specifically share the `*_loops.txt` handoff contract described in the repo's README. The Rap Sheet Builder's input is structurally unrelated — a **Most Wanted/Unwanted GeekList export**, which is a pre-completion demand snapshot, not a results file — and it doesn't produce or consume `*_loops.txt`. It shares no data contract with the numbered pipeline. It belongs in the same repo because it's still RVA-math-trade tooling and Troy wants one home for that family of tools, but it should read as a **sibling tool**, not a pipeline stage.

Suggested folder name: `richmonds-most-unwanted/` (or similar — Troy's call on exact naming), sitting at the same level as the numbered block folders, with its own script(s) and its own README section or file describing it as distinct from the 0→1→2→3 chain. It should NOT be wired into `run_genie.py`'s existing block-chaining menu as another sequential step — if it gets a harness entry point at all, it should be presented as a separate, standalone option, not folded into the "run 0 through 3 in sequence" flow.

One relevant piece of context Troy shared, worth capturing here in case it matters to naming/README framing: this tool is intended to run **once per math trade — after the final run**, not after every practice run the way Blocks 0–3 typically get used. That's a timing/usage-pattern distinction, not a data-architecture one — it doesn't change any of the technical rules above, but it's the kind of thing worth a line in whatever README section describes this tool.

---

## What Success Looks Like

- Running the tool against all 6 (soon 7) trade files in one pass produces: (a) per-trade QA metrics report, (b) the Rap Sheet.
- All per-trade sanity checks pass, or fail loudly with a clear message pointing at the offending trade/title.
- Any parsing anomalies (truncated files, malformed lines, near-duplicate titles differing by case/whitespace) are surfaced in a report, never silently swallowed.
- The tool is re-runnable as-is when the 7th trade (Summer 2026) file becomes available — dropping in a new file and its trade ID should be the only change needed.

## Explicitly Out of Scope for This Code Task
- Tiering logic ("true Most Unwanted" vs. second/third tier, streak scoring, recency weighting, owner-level tracking). This requires Troy to sit with the actual 6-trade Rap Sheet first, in Claude Chat, before those rules can even be written.
- Any public-facing communication of results.
- Cross-referencing BGG IDs or market value (that's Math Trade Genie's Block 3 territory, a separate tool).

---

*This document reflects rules tested and confirmed against 2023_03_MT, 2024_01_MT, and 2024_08_MT in a Claude Chat session. Trades 2025_01_MT, 2025_09_MT, 2026_01_MT have not yet been run through these rules — the Code build should apply the same logic to them, but Troy has not personally QA'd those three yet, so treat their per-trade metrics as freshly-generated-and-worth-double-checking rather than pre-verified.*
