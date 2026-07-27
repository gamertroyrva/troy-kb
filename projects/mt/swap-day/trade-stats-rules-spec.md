# Trade Stats — Rules Spec (Block 07)

*This document contains the business rules for the Trade Stats chore. Companion to `cash-consolidation-rules-spec.md` (Block 08) and `drop-zone-lists-rules-spec.md` (Block 09). All three draw from the Gives Receives Trade Log for a given math trade (see definition below). Block numbers 07–09 intentionally leave room for other blocks in between.*

**Explicitly OUT OF SCOPE:** turning this table into awards/superlatives (e.g., "most trades," "biggest redemption story"). That step is a narrative/judgment call Troy performs by hand, reviewing the table this block produces. This spec covers the data table only.

---

## Definition: The Gives Receives Trade Log

Every block in this family (07, 08, 09) draws from the same source document: **the Gives Receives Trade Log.**

When Troy runs the math trade algorithm for its final time, OLWLG generates a results webpage for that math trade. Each participant can scroll to their own section on that page and view their gives and receives — either side by side or gives-above-receives, participant's choice of layout. The page has additional content both above and below this per-participant section.

**The Gives Receives Trade Log is the critical middle portion of that page** — specifically, the per-participant gives/receives sections, extracted and pasted into a working document. It excludes the surrounding page content (headers, summary stats, footer material) above and below it.

Note: this document is deliberately NOT called "the results" or "the trade results" anywhere in this rules family, since "results" is heavily overloaded across this project (loop visualizations, awards tables, Working Copy outputs are all arguably "results" too). "Gives Receives Trade Log" is used consistently instead, to keep this specific artifact unambiguous.

The Gives Receives Trade Log is naturally alphabetized by BGG handle, since that's how OLWLG's page presents participants. This ordering is inherited, not independently applied — see Rule 3 below for how downstream blocks handle it.

---

## Purpose

Produce one reconciled reference table per math trade: for every participant, how many items they traded, how many they submitted, and how many of those were cash gives/receives. This table is the raw material Troy uses afterward to identify awards-worthy outcomes.

---

## Input

**One source document: the Gives Receives Trade Log** (defined above) — header line per participant plus numbered gives/receives lines.

*Note: an earlier draft of this spec called for a second source (a separate BGG participant-list export) as an independent cross-reconciliation check on Num Submitted. That approach was deliberately dropped — see Rule 3 for the reasoning and the self-consistency check that replaces it.*

---

## Rule 1 — Extract Per-Participant Counts from the Header Line

Each participant's header line has the form:
```
Handle : (N trades of M, P%) LOCATION [table format] [table no desc] [top]
```

- **Num Traded** = N (the first number). Includes everything — games and cash items both, no adjustment needed.
- **Num Submitted** = M (the second number, "of M").

---

## Rule 2 — Count Cash Gives and Cash Receives

For each participant, scan their `-- N. gives ...` lines and `++ N. receives ...` lines. Apply the existing **Cash Trade Identification Rule (v2)** (defined in `cash-consolidation-rules-spec.md`, Rule 1 — same logic, reused here as a counting filter rather than a netting or exclusion filter):

- **Num Cash Gives** = count of `gives` lines that pass the cash-trade test.
- **Num Cash Receives** = count of `receives` lines that pass the cash-trade test.

Do not double-apply the gift-card sub-rule differently here — a generic gift card counts as cash; a store-branded gift card does not, exactly as in Block 08.

---

## Rule 3 — Reconciliation Checks

Before treating the table as final, run these checks. If any fail, stop and flag the discrepancy — do not silently proceed.

**Why there's no second external source document:** Num Traded (N) and Num Submitted (M) are both self-reported inside the Gives Receives Trade Log's own header lines — OLWLG has already computed them correctly. The real risk isn't OLWLG's math; it's harvest/transcription corruption between the OLWLG webpage and the file this block reads (a truncated paste, a mangled section). A second external source (the BGG participant-list export) was considered for cross-reconciliation but deliberately dropped: it would catch a fully-vanished participant, but at the cost of parsing a second messy, wonky-to-harvest document. Troy's downstream workflow — pasting this table's output into his alphabetized Participants Google Sheet — already surfaces a fully-missing participant as a visible offset in the alphabetical order. That existing habit is an adequate safety net for the one gap the internal checks below don't cover.

1. **Internal self-consistency check, per participant:** the count of that participant's own gives + receives lines (as actually counted from the log) must equal their self-reported N value (Num Traded) from their own header line. A mismatch indicates a truncated or corrupted section for that participant specifically.
2. **Total Cash Gives (summed across all participants) must equal Total Cash Receives.** Every cash give is someone else's cash receive; the trade-wide totals must match exactly.
3. **Alphabetization validation:** confirm the Gives Receives Trade Log's participant order is alphabetical by BGG handle, as it should already be by construction (see Definition section above). This block does NOT sort or reorder — it validates the existing order and flags any anomaly (an out-of-sequence handle may itself indicate a harvesting error).

---

## Rule 4 — Output Table Structure

| BGG_Handle | Num Traded | Num Submitted | Num Cash Gives | Num Cash Receives |
|---|---|---|---|---|

- One row per participant who appears in the Gives Receives Trade Log (include participants with zero trades — do NOT apply Block 09's "omit if nothing to show" rule here; this is a complete reference table, not a print artifact).
- **Rows preserve the alphabetical-by-BGG-handle order inherited from the Gives Receives Trade Log** (validated per Rule 3, not independently re-sorted).
- **Include a Total row at the bottom**, summing every populated numeric column (Num Traded, Num Submitted, Num Cash Gives, Num Cash Receives). Do not sum BGG_Handle.

---

## Output

A single reconciled table in **CSV format**, structured per Rule 4, plus a pass/fail report on the Rule 3 checks (self-consistency, cash gives/receives balance, alphabetization). The CSV is designed for direct copy/paste into Troy's alphabetized Participants Google Sheet — column order and row order both matter for this to paste in cleanly. This table is handed back to Troy for the manual, narrative superlatives/awards pass — that step is not part of this block.

---

*Created: July 26, 2026, ahead of the Claude Code session to formalize Blocks 07–09. Revised same day: renumbered from 04 to 07; "trade" corrected to "math trade" for precision; source document formally named and defined (Gives Receives Trade Log); second reconciliation source dropped in favor of internal self-consistency + alphabetization validation; explicit alphabetical-ordering requirement added to output spec.*
