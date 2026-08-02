# Cash Consolidation — Rules Spec (Block 08)

*This document contains the complete business rules for the Cash Consolidation chore, standing on its own. Companion to `trade-stats-rules-spec.md` (Block 07) and `drop-zone-lists-rules-spec.md` (Block 09). Block numbers 07–09 intentionally leave room for other blocks in between.*

---

## Input

**The Gives/Receives Trade Log** — the per-participant gives/receives portion extracted from the OLWLG final results webpage for a given math trade. Each participant has a header line, a set of `-- N. gives "Item" to Handle` lines, and a set of `++ N. receives "Item" from Handle` lines. Full definition of how this document comes into existence lives in `trade-stats-rules-spec.md` (Block 07).

---

## Rule 1 — Cash Trade Identification

An item is a **cash trade** if and only if:

1. It is prefixed `"Alt Name:"`, **and**
2. The user-typed text (between "Alt Name:" and the trailing OLWLG item-id number) contains a **number** — the `$` symbol is optional, currency is assumed if omitted — **and**
3. Either:
   - The text contains a payment-method keyword: **Cash, PayPal, Venmo, or Zelle** (misspellings still count — match doesn't need perfect spelling), **or**
   - The text describes a **generic gift card with no store/brand name** (e.g., "$15 gift card").

**Exclusions:**
- An "Alt Name:" item with a payment keyword or generic gift-card language but **no number** is NOT a valid cash trade. It is a data-entry error to flag for correction in OLWLG — do not silently count it.
- A **store-branded gift card** (e.g., "$25 Target gift card," "Amazon gift card") is NOT a cash trade. Treat it as a physical item that must be hand-delivered at swap, like a board game.
- A real catalogued game with a `$` in its title (not prefixed "Alt Name:") is NOT a cash trade.

**Reconciliation check:** Total cash gives across all participants must equal total cash receives. If they don't match, stop and investigate before proceeding — do not build the NET Table or Working Copy on an unreconciled base.

---

## Rule 2 — The NET Table

For each participant with any cash activity:
- **GIVES** = sum of all cash items they give away
- **RECEIVES** = sum of all cash items they receive
- **NET** = RECEIVES − GIVES

Discard participants with zero cash activity (they have nothing to settle).

Split the resulting list into two independent columns:
- **GIVERS** — NET is negative (they owe money), shown as a negative number, sorted largest debt to smallest.
- **RECEIVERS** — NET is positive (they're owed money), sorted largest credit to smallest.

The absolute value of the Givers' total must equal the Receivers' total — a second reconciliation checkpoint.

**The NET Table is a required deliverable in its own right, not a disposable intermediate.** It is a human audit checkpoint that must be produced and reviewable on its own, even though its output feeds directly into Rule 3.

---

## Rule 3 — Matching Algorithm (produces the Working Copy)

Matching happens in passes, applied in order.

### Pass 1 — Exact 1:1
Scan for any giver whose debt amount exactly equals some receiver's credit amount (a clean 1:1 match). Pull every such pair out immediately and remove both parties from the pool.

*Rationale: these are unambiguous, zero-cost resolutions. Solving them first shrinks the remaining problem and avoids painting yourself into a corner later.*

### Pass 2 — Largest-Remaining-First, Either Side
With the low-hanging fruit removed, take whichever remaining party — giver or receiver — currently holds the single largest magnitude in the pool. The side isn't fixed in advance; work from whichever side the biggest number happens to be on.

- Find the exact-sum combination on the *other* side that closes it out.
- If no single counterpart matches, look for a subset of the other side that sums exactly to it.
- If no subset works either, pull in the next-largest remaining party on *this* side (combining debts or credits together) and re-search with the combined total. Repeat, pulling in additional parties one at a time, until a combined total matches some subset on the other side.
- Once a match is found, remove all matched parties from the pool and move to the next-largest remaining party (again, either side).

*Rationale: working from the biggest number in the remaining pool, whichever side it lives on, tends to produce the most natural groupings and avoids leaving small amounts stranded against an unresolvable large remainder later.*

### Pass 3 — Forced Splitting
When the remaining pool cannot be divided into clean, non-overlapping subset-sum clusters — no combination on either side closes the gap exactly — split one or more amounts into sub-pieces so that every dollar still lands on a valid pairing.

- Splits can occur on either side: a giver's debt can be divided across multiple receivers, and a receiver's credit can be fed by multiple givers.
- A single party's amount can even need to be split into pieces destined for different counterparts *because those counterparts each need a specific fragment of it* — not because the party's own total was too large to match anyone whole.
- There is no requirement that each giver's matched receivers form a sealed, exclusive group, or vice versa. Cash is fungible; a receiver getting their total from two unrelated givers is just as valid as getting it from one.

**Note on optimality:** this is a "mostly efficient," not a globally-optimal, matching process. If a different valid grouping exists that also nets to zero correctly, that is an acceptable outcome — exact amounts matching correctly matters far more than achieving the theoretical minimum number of transactions or the single "best" partition. Two different valid partitions of the same pool can both be correct.

---

## Rule 4 — Output Format: The Working Copy

Every row is a fully self-contained, atomic statement:

```
Giver | Amount | Receiver | Amount
```

where the two Amount values on a single row are always identical to each other. Giver-side amounts are shown as negative numbers.

- **No merged cells and no "total-at-top, breakdown-below" blocks.** If a party's total is divided across multiple counterparts, that party appears on as many separate rows as needed — one full (Giver, Amount, Receiver, Amount) line per dollar-slice. There are no orphaned totals and no blank-paired cells; every row stands on its own and can be independently verified without holding cluster context in your head.
- Rows belonging to the same settlement cluster stay grouped adjacent to one another, separated from the next cluster by a single blank spacer row. Clusters appear in the order they were solved — no magnitude-based sort is applied at this stage. Any reordering for readability is a presentation concern, deferred to the Recipe formatting step.

**Backlog / future improvement (not a current requirement):** find a way to display each cluster's running subtotal alongside its NET figure from Rule 2, so the reconciliation between a cluster's line items and its originating NET value can be verified visually rather than by mental math.

---

## Delivery

This chore is executed by a Python script, not built or maintained by hand in a spreadsheet. The script's output must be a **three-tab workbook**:

1. **NET Table** (Rule 2)
2. **Working Copy** (Rules 3–4)
3. **The Recipe** (rules not yet defined — separate spec, future session)

**Recommended mechanism: Excel (.xlsx), written locally via a library such as `openpyxl` or `xlsxwriter`.** This requires no authentication or live API calls — the script runs standalone and produces a file, which is the simpler and more reliable path for a pure data-processing job.

Getting the result into Google Sheets (Troy's preferred living environment) is a separate, optional last step, not a constraint on how the script itself works:
- **Manual:** upload the finished .xlsx to Drive; Google auto-offers conversion to a native Sheet on upload.
- **Automated:** have the script call the Drive API's file-creation endpoint with conversion enabled, landing the workbook directly as a native Google Sheet without a manual upload step.

Either delivery path uses the identical tab-building logic above; only the final "where does the finished workbook go" step differs.

---

*Created: July 24, 2026. Revised July 27, 2026, following joint analysis of two independent math trades' cash consolidation data.*
