# Item 13: Andrew's Driving Service — All Destinations Spreadsheet
*Handoff document. Written May 17, 2026. For Troy or any future Claude session picking this up cold.*

---

## What This Is

Andrew runs a driving service (Andrew's Driving Service) in central Virginia. His business has two flavors of destinations:

- **Fun destinations** — wineries, breweries, cideries, distilleries, meaderies across Virginia. These are the weekend warrior trips, the bachelorette parties, the wine trail excursions. The reason people hire a driver.
- **Functional destinations** — airports, medical facilities, and the like. Weekday revenue. Keeps the lights on.

Andrew built a master spreadsheet — **CEN_VA_MASTER_CURRENT_1_.xlsx** — to catalog all of his destinations. He got it all the way to 280 rows and 93 columns. It is a serious, ambitious piece of work. It has multiple supporting sheets: Project Notes, Column Checklist, Documentation, Expansion Policy, Policy Notes. The man has *process*.

But the spreadsheet has data quality problems that eroded his confidence in it. And an earlier bad experience with AI trying to help — which made things worse, not better — caused him to largely abandon the quest to make it a living, breathing business asset.

**Troy's mission:** Restore Andrew's faith in AI as a business partner by actually solving the real problems with this spreadsheet.

---

## The Human Story

- Andrew and Troy connected in mid-May 2026.
- Friday May 15: "life-altering phone call" — Andrew shared his situation, Troy offered to help.
- Saturday May 16, 6am: Andrew screen-shared his spreadsheet live. Troy mostly listened. Key move.
- Troy captured Andrew's confidence problems in real time (Andrew couldn't believe Troy was writing them down — that mattered).
- A letter from Claude to Andrew was drafted and sent immediately after the Saturday call. It was well-received. Faith-restoration has begun.

---

## Why Andrew Lost Confidence in the Spreadsheet

Troy captured this list directly from Andrew during the Saturday call. These are Andrew's own words, essentially:

1. **Blank cells** — incomplete records throughout
2. **Duplicates** — same venue appearing multiple times
3. **Human comparison finding mistakes and inaccuracies** — when Andrew manually checked entries, he found errors
4. **Was going to start over and do it one venue at a time** — the nuclear option; he'd considered it
5. **It missed venues** — venues exist that aren't on the list at all

This is a **data quality problem**, not a spreadsheet design problem. And AI is well-suited to exactly this class of problem — which is part of the rehabilitative angle here.

---

## What the Spreadsheet Actually Contains (as of May 17, 2026)

**File:** CEN_VA_MASTER_CURRENT_1_.xlsx  
**Sheets:** Venues (main), Project Notes, Column Checklist, Documentation, Expansion Policy, Expansion Venues, Policy Notes

### Venues Sheet — By the Numbers
- **280 rows** of data (row 1 = headers)
- **93 columns** (columns A through CO)
- **Venue breakdown:** Winery (220), Brewery (31), Cidery (13), Distillery (4), Meadery (2), mixed types (10)
- **Status:** 258 Open, ~17 Closed or Invalid in various forms
- **Duplicate venue names:** 39 venue names appear more than once — confirmed duplicate problem
- **Gap Count (col AH):** Every single row has a gap count. Min: 21, Max: 70, Average: 45.5 out of 93 fields. Zero fully complete rows.

### Notable Column Patterns
Andrew is detail-oriented to an impressive degree. Examples of the nuance built into this schema:

- **Col M:** "Pet Friendly" — narrative field (e.g., "Dog friendly outdoors")
- **Col N:** "Pet-Friendly" — yes/no flag (largely empty)
- **Col O:** "Pets Policy (Details)" — full policy text (largely empty)

Same pattern repeats for kids, reservations, accessibility, and more. The *intent* is layered detail. The *reality* is that the detailed sub-fields are mostly blank while the narrative fields have partial info. This is a normalization/enrichment opportunity.

- **Col AH (Gap Count):** Andrew or a prior AI session added a gap audit column. It counts blank fields per row. This is incredibly useful — it's a built-in priority queue for gap-filling work.
- **Col AJ (Reconciliation Flag):** For marking duplicate/ALT entries (e.g., "ALT_OF: [Primary Venue]")
- **Cols AL–AN:** Underscore-prefixed columns (_domain, _canon, _addr, _phone) — these look like normalized/canonical versions of key fields, possibly added during a prior AI-assisted cleanup attempt
- **Col AZ (Last Verified/Updated), BA (Last Verified/Updated ET), BB (Verified By):** Andrew built a verification audit trail into the schema. The intent was there. The execution stalled.

### Other Sheets Worth Knowing
- **Project Notes:** Has a full documented process flow — load file, take next row with gaps, deep-dive research, normalize, stamp verified. Andrew knew exactly what he wanted to do. He just ran out of steam.
- **Column Checklist:** A checklist of all columns for the deep-dive process — used during row-by-row enrichment sessions.
- **Documentation:** Tracks specific rows that have been verified, with sources cited. Very few rows are in here.
- **Expansion Policy:** Formal rules for what qualifies to be added (wineries, breweries, distilleries, cideries, meaderies, sub-venues on those properties — no standalone restaurants).

---

## Troy's Prototype Spreadsheet

Troy built a prototype spreadsheet prior to the Saturday 6am call, with Claude's help. It was brought to the meeting as a starting point / conversation piece. During the meeting, Troy mostly listened rather than pitching the prototype — the right call. The prototype's current state and column structure should be documented here once Troy surfaces it.

*(Troy: add prototype details here when you pick this back up)*

---

## What's In It for Andrew

1. A living, breathing master spreadsheet of all destinations he can trust, update, and actually use
2. Restored faith in AI as a business partner
3. Specific fixes to each of his five confidence problems (blanks, duplicates, inaccuracies, missing venues, coverage gaps)

## What's In It for Troy

Andrew's Driving Service is **client zero** in Troy's emerging AI automation practice (Liam Otley model — build real AI solutions for real clients, productize the workflow). This is the portfolio piece. Not a revenue target right now — Troy is in foundation-building mode with a day job and a full AI learning journey already consuming enormous hours. But when this works, it works for the next client too.

---

## The Data Quality Problems — Mapped to Solutions

| Andrew's Problem | What It Looks Like in the Data | Likely Solution Approach |
|---|---|---|
| Blank cells | Avg 45.5 gaps per row; Gap Count column already exists | AI-assisted enrichment using web research per venue |
| Duplicates | 39 venue names appear 2+ times | Deduplication pass; use Reconciliation Flag col already in schema |
| Human finding mistakes | Address duplication in address field (e.g., "234 Vineyard Lane, Afton, VA 22920, Afton, VA 22920") | Validation layer; address normalization |
| Missing venues | Unknown count; Andrew knows some are missing | Discovery pass against VirginiaWine.org and similar sources |
| Incomplete coverage | Most detailed sub-fields (pet policy details, kids policy details, etc.) are blank | Structured enrichment using venue websites + AI |

---

## What Needs to Happen Next (When Troy Picks This Up)

1. **Decide on the target schema.** 93 columns is a lot. Some are redundant (M/N/O pet situation). The first conversation when resuming should be: which columns survive into the cleaned master, and what does the canonical schema look like?

2. **Surface and review Troy's prototype spreadsheet.** Compare its schema to Andrew's. What did the prototype get right that Andrew's doesn't have? What did Andrew build that the prototype missed?

3. **Duplicate resolution.** Run a dedup pass. Albemarle CiderWorks appears 3 times, Blue Mountain Brewery appears 3 times, Barboursville Vineyards appears 3 times. These need reconciliation using the existing Reconciliation Flag column convention Andrew already designed.

4. **Address normalization.** Several addresses appear doubled (city/state appended twice). Clean these.

5. **Gap-filling strategy.** With 280 rows and average 45 gaps each, you can't do this manually. This is where AI-assisted enrichment — web research per venue, structured output, write back to sheet — becomes the core of the project.

6. **Missing venues discovery.** VirginiaWine.org is cited in Andrew's Documentation sheet as a source. Likely the best starting point for a coverage audit.

---

## Andrew's Contact Context

- Andrew is not a spreadsheet power user. He built this because he *needed* it, not because he loves spreadsheets.
- He tried AI before and it did not go well. Trust must be earned back through results, not promises.
- He is detail-oriented (93 columns is not an accident).
- The Saturday morning call was his opening up about a problem he'd largely given up on.
- The letter from Claude sent after that call was a strong first move. The relationship is warm.

---

## Files

- **Andrew's spreadsheet:** CEN_VA_MASTER_CURRENT_1_.xlsx (uploaded to Claude May 17, 2026)
- **Troy's prototype:** *(to be added)*
- **The letter from Claude to Andrew:** *(exists in the exhausted Friday/Saturday chat thread — worth recovering if possible)* *Troy note: easily recovered from Gmail Sent items folder. *

---

*This document was created May 17, 2026 during a 30-minute packaging sprint before Troy shifted focus to Item (MisoCon26 Attendee Tracker). Pick up THIS item fresh when time allows.*
