# Per-Participant Drop Zone Lists — Rules Spec

*This document contains the business rules for the Drop Zone Lists chore. Companion to `cash-consolidation-rules-spec.md`. Both draw from the same raw source Doc for a given trade.*

---

## Purpose

Take the same raw per-participant trade export (Google Doc) used for Superlatives and Cash Consolidation, and produce a trimmed, print-and-cut set of personal "drop zone" labels — one per eligible participant — showing only what they're set to receive at physical swap.

---

## Input

Same raw per-participant trade export used elsewhere: header line per participant (stats + optional location), followed by numbered `-- N. gives ...` and `++ N. receives ...` lines.

---

## Rule 1 — Strip to Receives Only

Delete all `-- N. gives ...` lines entirely. A drop zone is about what arrives for pickup, not what the participant is shipping out. Only `++ N. receives ...` lines remain.

---

## Rule 2 — Strip Location Suffixes

Remove any location text attached to handles (e.g. "RICHMOND, VA, USA", mileage annotations). These cause text wrapping in the printed output and hurt at-a-glance readability. This applies to both the participant's own header line and to the "from Handle" attribution on each receive line.

---

## Rule 3 — Remove Cash Items (Reuse Cash Trade Identification Rule)

Delete any receive line that qualifies as a cash trade under the existing **Cash Trade Identification Rule (v2)** from the Cash Consolidation spec — same rule, same logic, applied here as a *filter* rather than a *counter*.

**Do NOT delete every "Alt Name:" item** — only ones that pass the full cash-trade test (prefixed "Alt Name:", contains a number, contains a payment keyword or is a generic/unbranded gift card). Non-cash "Alt Name:" items — e.g. "Miscellaneous Game Accessory," "Outside the Scope of BGG," a store-branded gift card — are physical items that still need to be handed over at swap, and must remain in the list.

Cash items are handled separately through the Cash Consolidation Working Copy (The Recipe) — they don't belong in a physical drop zone.

---

## Rule 4 — Omit Participants With Nothing to Collect

**Do not generate a drop zone section for any participant who has zero trades, or whose receives list is empty after Rules 1–3 are applied** (e.g., every item they were due to receive turned out to be cash, filtered out by Rule 3). No empty header, no blank box — the section is omitted entirely for that person.

*This must be stated explicitly and not left to inference. Without this rule spelled out, a script (or a future pass by Claude) could reasonably choose to print an empty labeled box for such a participant instead of omitting them — a small unstated liberty that would drift the output from what's actually wanted.*

---

## Rule 5 — Preserve Original Item Numbering

When lines are deleted (gives, cash items), **do not renumber the remaining receive lines to close the resulting gaps.** A participant who originally had items 1–8 and loses items 3 and 6 to filtering should show 1, 2, 4, 5, 7, 8 — not renumbered 1–6.

*Rationale: preserves traceability back to the original OLWLG item ID for each remaining line. Renumbering would sever that connection.*

---

## Rule 6 — Preserve the Original Stats Line As-Is

The participant's header stats line (e.g. "(N trades of M, P%)") is carried over unchanged from the raw Doc. It reflects the participant's *original* full trade activity (gives + receives + cash), not a recalculated count of the trimmed receives-only list shown beneath it. Do not recompute this figure.

---

## Rule 7 — Header Styling

The participant's username, appearing above their trimmed receives list, is formatted large (Arial 36), bold, and blue — sized for legibility from a short physical distance at the swap table.

---

## Rule 8 — Spacing / Widow Prevention (NOT Automated — Human Judgment Step)

After the above rules produce the trimmed, filtered document, a manual pass is applied in Google Docs to:
- Prevent a participant's list from splitting awkwardly across a page break
- Add variable spacing between consecutive participants, especially where several people in a row have short (low-item-count) lists, so that the eventual print-and-cut into individual slips produces well-proportioned physical pieces

**This step is explicitly out of scope for automation at this time.** It depends on visual judgment of the rendered page layout — how content happens to fall across pages, and what looks right once printed and cut with scissors — which isn't reliably reducible to a rule without seeing actual rendered output. The output of Rules 1–7 should be treated as a complete, correct *input* to this manual step, not as the final print-ready artifact.

---

## Output

A single document, one section per eligible participant (Rule 4), each formatted per Rules 5–7, in the same order as the original raw Doc (alphabetical by handle). This is an intermediate deliverable — final print-ready formatting still requires the Rule 8 manual pass before it's cut into physical drop-zone slips.

---

*Created: July 25, 2026, during the July RVA no-ship trade session, using the January trade's raw Doc and finished drop-zone Doc as the comprehension pair.*
