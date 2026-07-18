# Richmond's Most Unwanted — Analysis Rules (v1)
*A history marker on Mechanicsville Turnpike. First trade processed: 2023_03_MT.*
*Expect a v2 once trade 2 enters the building.*

---

## The Six Trades

| Trade Name | ID |
|---|---|
| Richmond VA 2023 March No-Ship | 2023_03_MT |
| Richmond VA 2024 January No-Ship | 2024_01_MT |
| Richmond VA 2024 August No-Ship | 2024_08_MT |
| Richmond VA 2025 January No-Ship | 2025_01_MT |
| Richmond VA 2025 September No-Ship | 2025_09_MT |
| Richmond VA 2026 January No-Ship | 2026_01_MT |

---

## Scope

- Source data per trade: a GeekList export with a **MOST WANTED** section (ranked, one line per listing) and an **UNWANTED** section (alphabetical, one line per listing).
- **Alt Name entries** (cash/PayPal/Venmo placeholders, or non-cataloged upgrade/accessory listings tagged "Alt Name:") are **out of scope entirely** — excluded from every count, in both sections.

## Matching

- Game identity is matched on **exact title text as printed on the GeekList** — no fuzzy grouping, no collapsing of related titles.
  - "Munchkin" and "Munchkin Disney" are different games.
  - Two expansions of the same base game are different games.
  - Different printings/copies of the *same* title (different owners, different per-trade listing IDs) *are* the same game.

---

## Per-Trade Processing (Single-Trade View)

This is the simplified, single-trade snapshot. It uses a shortcut rule that does **not** carry forward into the global analysis (see below).

**Rule 1 (Wanted overrides Unwanted, single-trade only):** If a title appears anywhere in MOST WANTED for a trade, it is WANTED for that trade — full stop — even if it also appears in UNWANTED one or more times in that same trade. A title's final status per trade is therefore binary: WANTED or UNWANTED, never both.

**Per-trade metrics (tight 4-row table, plus the bridge when useful):**

1. **GeekList Items in the trade** — raw count of every listing line across both sections, including Alt Name entries and every duplicate title as its own line. Should match the trade's own published total (verified exactly for 2023_03_MT: 525). Human being will audit this if they want to.
2. **Total unique game titles in the trade** — GeekList Items, minus Alt Name entries, then deduped by exact title.
3. **Wanted** — unique titles found anywhere in MOST WANTED.
4. **Unwanted** — unique titles found in UNWANTED and nowhere in MOST WANTED (survived Rule 1).

Sanity check every time: Wanted + Unwanted = Total unique game titles.

**QA checks available right now, per trade:**
- Does GeekList Items match a number you already know from another source?
- Do the three summary numbers reconcile?
- Spot-check any title that seems like it should've been overridden by Rule 1 but wasn't (or vice versa).

---

## Global Processing (Across All 6 Trades) — Different Rules

The single-trade Rule 1 shortcut is **deliberately abandoned** at the global level. Collapsing wanted/unwanted per trade would erase the exact story this analysis exists to tell.

- **Analytical Universe:** the set of titles that were **unwanted at least once, in at least one trade.** Titles that were always wanted, every time they appeared, never enter this universe — they're irrelevant to Richmond's Most Unwanted.
- **Per-title, per-trade status:** a **three-state flag** — **Wanted / Unwanted / Absent** — for every title in the Analytical Universe, for each of the 6 trades. No sub-counting of multiple copies within a trade; one flag per title per trade is the full granularity needed.
- **The headline "Most Unwanted" list:** titles in the Analytical Universe that were **Unwanted every time they appeared, and never once Wanted** (Absent trades don't break the streak).
- **The "comeback kid" middle ground:** titles that were Unwanted at least once but also Wanted at least once elsewhere — tracked as its own distinct category, separate from the hall-of-shame list.

---

## Not Yet Decided (parked, no action needed yet)

- Exact data structure/tooling for storing the 6-trade flag grid per title (spreadsheet? Google Sheet? something else?).
- Any threshold or additional criteria for what counts as a "repeat offender" beyond "unwanted every time it appeared."
- Whether/how findings ever get communicated to the community — flagged early as risky, since "unwanted in past trades" doesn't mean "nobody will ever want it."

---

*Created: July 4, 2026, during processing of 2023_03_MT (trade 1 of 6).*
