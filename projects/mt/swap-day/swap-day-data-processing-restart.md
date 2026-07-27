# Swap Day Data Processing — Restart Doc

*Load this alongside the standard session-start files. This doc covers ONE project: the data processing prep work Troy does between Sunday (OLWLG results finalized) and Saturday 10am (swap day) for each local no-ship RVA math trade.*

---

## The Big Picture

Troy runs this same data processing gauntlet after every local math trade, on a tight Sunday-results-to-Saturday-swap window. Right now it's manual and happens too close to swap day (this round, it's happening Friday morning — later than ideal). The end goal is a repeatable toolset — eventually a Python script built with Claude Code, with formalized business rules — so this entire process takes minutes on a Monday instead of a scramble late in the week. This session's job is partly to get THIS trade's outputs done, and partly to nail down business rules clean enough to become that script's spec.

There are **three data processing chores** per trade cycle:

### 1. Superlatives / Awards Data (mostly complete for this trade)
### 2. Cash Consolidation (in progress — today's priority, needed before work this AM)
### 3. Per-Participant Drop Zone Lists (not started — no rush)

---

## Chore 1: Superlatives / Awards Data — DETAILED

**Purpose:** Mine trade data for exceptional outcomes to fashion into awards announced live at the swap.

**Source data:** A Google Doc export from OLWLG/BGG containing, for each participant, a header line like:
```
kristybeardesigns : (12 trades of 18, 66%) RICHMOND, VA, USA  [table format] [table no desc] [top]
```
followed by numbered `-- N. gives "Item (id)" to Handle (id) [location]` lines and `++ N. receives "Item (id)" from Handle [location]` lines.

**Target table structure:**

| BGG_Handle | Num Traded | Num Submitted | Num Cash Gives | Num Cash Receives |
|---|---|---|---|---|

- **Num Submitted** = the "M" in the header's "(N trades of M, P%)" — reconciles against a separate BGG participant-list export's "Item Count" column.
- **Num Traded** = the "N" in "(N trades of M, P%)" — includes everything, games and cash items both, no adjustment needed.
- **Num Cash Gives / Num Cash Receives** = counted via the codified business rule below.

**CODIFIED BUSINESS RULE — Cash Trade Identification:**
> An item is a **cash trade** if and only if:
> 1. It is prefixed `"Alt Name:"`, **and**
> 2. The user-typed text (everything between "Alt Name:" and the trailing OLWLG item-id number) contains a **number** — the `$` symbol is optional, currency can be assumed if omitted — **and**
> 3. The user-typed text contains at least one payment-method keyword: **Cash, PayPal, Venmo, or Zelle** (misspellings like "PayPayl" still count — the keyword match doesn't need to be a perfect spelling).
>
> If an "Alt Name:" item has a payment keyword but NO number at all, it is NOT a valid cash trade — it's a data-entry error that must be corrected in OLWLG before the trade can proceed. This rule does not silently count it.

**Key traps this rule correctly avoids:**
- `"Alt Name: Miscellaneous Game Accessory"` and `"Alt Name: Outside the Scope of BGG"` — both use the generic "Alt Name:" wrapper (OLWLG's marker for "not a real catalogued BGG item") but are NOT cash. No number, no payment keyword → correctly excluded.
- `"Ca$h 'n Guns: Second Edition"` — a real catalogued game with a literal $ in the title, but NOT prefixed "Alt Name:" → correctly excluded. (The rule requires the "Alt Name:" prefix, not just a $ sign.)

**Reconciliation checks used along the way (worth reusing for future trades):**
- Total Cash Gives across all participants should equal Total Cash Receives (every cash give is someone else's cash receive).
- Sum of "M" values across all headers should equal the participant-count table's total.
- Handle list from the two data sources should match 1:1 with no extras either direction.

**Status:** This table is built and reconciled for the current (July) trade. Not yet turned into awards/superlatives — that analysis step hasn't started.

**Display preference:** Any future display of this table should include a **Total row at the bottom** summing all populated numeric columns.

---

## Chore 2: Cash Consolidation — IN PROGRESS, TODAY'S PRIORITY

**Purpose:** Take all the individual cash gives/receives scattered across the trade loops and boil them down to the **minimum number of actual cash payments** needed so everyone's obligations net out — instead of everyone paying/collecting piecemeal per-loop.

**Reference artifact:** Troy has a Google Sheet from the most recent prior no-ship trade (January) with 7 tabs:
- **Leftmost tab:** raw data entry — individual cash sums by participant.
- **Middle tabs:** audit/working steps showing the process of collapsing the raw data down.
- **Rightmost tab:** the final target — smallest number of cash exchanges needed to resolve the trade's cash flow.

**IMPORTANT:** This January sheet is a **different trade** — different participants, different numbers, same *format* only. Do not confuse it with the current (July) trade's data. This is exactly why this work moved to a fresh chat session — to avoid any risk of the two datasets bleeding into each other.

**Proposed 3-phase approach for working with this reference sheet (Troy's plan):**
1. **Phase One — Comprehension.** Ingest the January sheet, discuss it, make sure Claude understands the shape of raw input and target output. Claude does NOT need to replicate Troy's exact tab-by-tab process — only needs to understand the rules for reading the raw input and the definition of the correct final output.
2. **Phase Two — Production.** Claude produces the final consolidated-cash-exchange output for the CURRENT (July) trade's cash data, by whatever method it chooses. Troy reviews for correctness.
3. **Phase Three — Auditability.** Once Troy sees the output looks right, work out how to verify mathematical accuracy without Troy having to micromanage the method. (Troy is optimistic Claude can do this correctly without heavy oversight, but wants some audit mechanism in place.)

**Status:** Not yet started in the new session. Next step is Troy providing the January reference Google Sheet.

---

## Chore 3: Per-Participant Drop Zone Lists — NOT STARTED, NO RUSH

**Purpose:** Take the same source Google Doc (the OLWLG gives/receives export) and, for each of the participants, strip it down to just their own relevant info, reformatted into a clean personal list — supporting each person having their own labeled drop zone at the physical swap.

**Known so far:** Involves removal of extraneous info and reformatting from the same input Google Doc used for Chore 1. No business rules or format specifics have been discussed yet. This is a "put it on the radar" item for today, not a same-day deliverable.

---

## Where This Is Headed

Once the business rules across all three chores are solid and proven out manually a couple more times, the intent is to formalize them and build a Python script (likely with Claude Code) so that this entire three-chore data processing pipeline runs in minutes each Monday after a trade closes Sunday — rather than being reconstructed by hand under time pressure late in the week. This fits as a natural extension of the Math Trade Genie tool family (alongside Blocks 1–3) and the broader AI-Assisted Math Trade System vision.
