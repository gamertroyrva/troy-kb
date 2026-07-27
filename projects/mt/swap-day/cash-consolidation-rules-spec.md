# Cash Consolidation — Rules Spec (Block 08) (The Recipe / Working Copy)

*This document contains only the business rules for the Cash Consolidation chore. No example data, no prior answers, no math-trade-specific numbers. Intended for use in an isolated session to test whether the rules alone are sufficient to reproduce a correct result. Companion to `trade-stats-rules-spec.md` (Block 07) and `drop-zone-lists-rules-spec.md` (Block 09). Block numbers 07–09 intentionally leave room for other blocks in between.*

---

## Input

**The Gives Receives Trade Log** — the per-participant gives/receives portion extracted from the OLWLG final results webpage for a given math trade. Each participant has a header line, a set of `-- N. gives "Item" to Handle` lines, and a set of `++ N. receives "Item" from Handle` lines. Full definition of how this document comes into existence lives in `trade-stats-rules-spec.md` (Block 07).

---

## Rule 1 — Cash Trade Identification (v2)

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

**Reconciliation check:** Total cash gives across all participants must equal total cash receives. If they don't match, stop and investigate before proceeding — do not build the Working Copy on an unreconciled base.

---

## Rule 2 — Build the NET Table

For each participant with any cash activity:
- **GIVES** = sum of all cash items they give away
- **RECEIVES** = sum of all cash items they receive
- **NET** = RECEIVES − GIVES

Discard participants with zero cash activity from further steps (they have nothing to settle).

Split the resulting list into:
- **Givers** — NET is negative (they owe money)
- **Receivers** — NET is positive (they're owed money)

The absolute value of the Givers' total must equal the Receivers' total. This is a second reconciliation checkpoint.

---

## Rule 3 — Matching Algorithm (produces the Working Copy)

Matching happens in passes. Apply them in order, but be willing to move between Pass 2 and Pass 3 as needed — they're complementary tactics for the same remaining pool, not a strict one-way pipeline.

### Pass 1 — Low-Hanging Fruit
Scan for any giver whose debt amount exactly equals some receiver's credit amount (a clean 1:1 match). Pull every such pair out immediately and remove both parties from the pool.

*Rationale: these are unambiguous, zero-cost resolutions. Solving them first shrinks the remaining problem and avoids painting yourself into a corner later.*

### Pass 2 — Smallest-Remaining-Giver-First
With the low-hanging fruit removed, take whatever givers and receivers are left. Process givers **smallest debt first**:

- For the smallest remaining giver, find a combination of remaining receivers whose credits sum exactly to that giver's debt.
- Remove the matched receivers from the pool once used.
- Move to the next-smallest remaining giver and repeat.

*Rationale: starting with the smallest giver first (rather than the largest) avoids leaving small, hard-to-match receiver amounts stranded against a large remaining giver at the end.*

**If the smallest remaining giver has no exact-sum receiver subset available:** don't stall. Combine that giver with the next-smallest remaining giver (summing their debts together) and search again. Repeat — pulling in additional small givers one at a time — until a combined debt total matches some subset of remaining receivers. Once found, split the resulting payment back across the individual givers involved (see Pass 4).

### Pass 3 — Largest-Remaining-Receiver-First, Gap-Filled
This is the mirror image of Pass 2, and often the more natural move for the *biggest* numbers in the pool:

- Take the largest remaining receiver's credit amount.
- Take the largest remaining giver's debt amount.
- If the giver's debt is smaller than the receiver's credit, find the exact giver (or combination of givers) whose debt closes that specific gap — not just any leftover amount, the precise shortfall.
- Once the combined giver debts exactly equal the receiver's credit, that group is settled and removed from the pool.

*Rationale: for large givers and large receivers, hunting for a receiver-side subset (Pass 2's approach) is often harder than simply asking "what closes this specific gap?" Working top-down from the biggest numbers first tends to produce tighter, more natural-feeling groupings for the largest amounts in the trade.*

### Pass 4 — Splitting a Shared Receiver or Giver Across a Cluster
When a cluster resolves with more than one giver and/or more than one receiver (from Pass 2's combined-giver fallback or Pass 3's gap-fill), the individual payment lines within that cluster don't have to preserve strict "this giver only pays receivers in its own private group" boundaries. A single receiver's total credit **can** be fed by two or more unrelated givers, and a single giver's total debt **can** be split across two or more unrelated receivers, whenever doing so produces a cleaner or more natural-looking result than forcing rigid, mutually exclusive giver-to-receiver groupings.

*Rationale: earlier versions of this rule treated each giver's matched receivers as a sealed, exclusive group. That's unnecessarily restrictive — cash is fungible, and a receiver getting their $45 from two different people is just as valid a real-world payment as getting it from one. Allowing splits across the cluster boundary produces rounder, more efficient groupings.*

**Note on optimality:** this is a "mostly efficient," not a globally-optimal, matching process. If a different valid grouping exists that also nets to zero correctly, that is an acceptable outcome — exact amounts matching correctly matters far more than achieving the theoretical minimum number of transactions.

---

## Rule 4 — Final Display Order

Once all matches are made (regardless of which pass produced them), the finished Working Copy should be sorted as a single list:

- **By giver, largest total debt to smallest total debt.**
- Each giver's own matched receiver lines stay grouped together beneath their name, in the order they were resolved.

---

## Output

A flat list of individual payment lines:

```
Giver | Amount | Receiver
```

This is **the Working Copy** (a.k.a. "The Recipe" in its math-complete form) — the minimum-effort set of real-world cash payments that settles the trade's cash flow. Formatting, name enrichment (real names, proxy annotations, etc.) for swap-day presentation happens as a separate, later step and is out of scope for this spec.

---

*Created: July 24, 2026, during the July RVA no-ship trade cash consolidation session.*
