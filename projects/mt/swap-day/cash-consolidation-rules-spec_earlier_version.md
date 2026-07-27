# Cash Consolidation — Rules Spec (The Recipe / Working Copy)

*This document contains only the business rules for the Cash Consolidation chore. No example data, no prior answers, no trade-specific numbers. Intended for use in an isolated session to test whether the rules alone are sufficient to reproduce a correct result.*

---

## Input

A raw per-participant trade export (Google Doc format) where each participant has a header line, a set of `-- N. gives "Item" to Handle` lines, and a set of `++ N. receives "Item" from Handle` lines.

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

Matching happens in two passes, in this order:

### Pass 1 — Low-Hanging Fruit
Scan for any giver whose debt amount exactly equals some receiver's credit amount (a clean 1:1 match). Pull every such pair out immediately and remove both parties from the pool.

*Rationale: these are unambiguous, zero-cost resolutions. Solving them first shrinks the remaining problem and avoids painting yourself into a corner later.*

### Pass 2 — Smallest-Remaining-Giver-First
With the low-hanging fruit removed, take whatever givers and receivers are left. Process givers **smallest debt first**:

- For the smallest remaining giver, find a combination of remaining receivers whose credits sum exactly to that giver's debt.
- Remove the matched receivers from the pool once used.
- Move to the next-smallest remaining giver and repeat.
- Continue until all givers and receivers are exhausted.

*Rationale: starting with the smallest giver first (rather than the largest) avoids leaving small, hard-to-match receiver amounts stranded against a large remaining giver at the end. This ordering has empirically produced a fully clean resolution with no backtracking required.*

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
