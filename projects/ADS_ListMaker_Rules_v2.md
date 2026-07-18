# ListMaker — Decision Log & Rules
*Andrew's Driving Service (ADS) / RowBuilder project*
*Updated June 21, 2026, following the trail-harvest session that produced the first 121-row candidate list.*

---

## What ListMaker Is

ListMaker is the harvesting/seeding tool that builds the candidate venue work queue. It is **not** a draft of Andrew's master spreadsheet — it's a checklist RowBuilder grinds through, venue by venue, to actually realize the ADS location database.

---

## Geographic Scope

- **State:** Virginia only.
- **Method:** an explicit list of counties and independent cities — not a literal mileage radius. A radius drawn from a single center point doesn't reliably reproduce an intended boundary (north-south position affects straight-line distance as much as east-west, so a circle can include/exclude places counterintuitively). The boundary is a county-by-county judgment call.
- **Drafting aid only:** a 60-mile circle centered on Charlottesville was used as a visual guide while making county-by-county decisions. It is not itself a rule.

### Counties / Independent Cities IN scope (26)

- Albemarle
- Charlottesville (city)
- Greene
- Madison
- Nelson
- Fluvanna
- Buckingham
- Amherst
- Cumberland
- Goochland
- Powhatan
- Augusta
- Staunton (city)
- Waynesboro (city)
- Rockingham
- Harrisonburg (city)
- Shenandoah
- Page
- Rappahannock
- Culpeper
- Orange
- Louisa
- Spotsylvania
- Rockbridge
- Lexington (city)
- Buena Vista (city)
- Appomattox

### Notable exclusions (deliberate calls, not defaults)

- **Henrico County** — excluded despite sitting right next to Goochland/Richmond
- **Chesterfield County** — excluded despite sitting right next to Powhatan/Richmond
- **Fredericksburg (city)** — excluded
- **Bedford County** — excluded
- **Bedford City** — excluded
- **Caroline County** — excluded
- **Hanover County** — excluded, reconsidered explicitly; a real chunk of it falls inside the 60-mile circle but it was cut anyway
- **Lynchburg (city)** — excluded
- **Prince Edward County** — included in an earlier pass, removed after a second look
- **Amelia County** — included in an earlier pass, removed after a second look

Everything not listed as IN above is OUT by default.

---

## Venue Eligibility Rules

Source: Andrew's own Expansion Policy (already documented in his master file before this session).

**Eligible venue types:**
- Wineries
- Breweries
- Distilleries
- Cideries
- Meaderies
- Event sub-venues *within* those properties (tasting rooms, barrel houses, winery restaurants)

**Excluded:**
- Standalone restaurants/cafes, unless tied to a licensed alcohol venue
- Hospitals, airports, and general "functional" destinations — confirmed out of scope entirely

---

## ListMaker Output Fields (7)

| Field | Why it's in ListMaker's scope |
|---|---|
| Venue | Primary identity field — the one truly mandatory field. See Scope Discipline below. |
| Address | Full offload to RowBuilder — static fact, worth the harvest cost when a source provides it; not chased down when it doesn't. |
| City/Town | Static fact, captured when the source provides it. |
| ZIP | Static fact, captured when the source provides it. |
| County | Static fact; also doubles as the in-scope/out-of-scope check. May be derived from a given City/Town using general geography knowledge — this is not a new fetch, just applied knowledge. |
| Primary Venue Type | Single-value enum (Winery/Brewery/Cidery/Meadery/Distillery/Other). When a venue's name or sourcing suggests more than one type, default to the first-listed type in the venue's own name. |
| Wine Trail/Association | Trail or industry association membership. Comma-separated when a venue belongs to more than one, ordered by Pecking Order (below), not by harvest order. |

**Deliberately excluded from ListMaker's output:**
- **State** — constant across this entire VA-only project, zero disambiguating value
- **Phone** — an attribute, not an identity field; doesn't belong in a primary-key-type list
- **Status** (open/closed) — always needs a fresh check at RowBuilder run time regardless of what's harvested
- **Production Types / Venue Type Enhanced** — deeper classification fields that stay RowBuilder's job, not ListMaker's
- **Website** — not in scope for ListMaker

---

## Pecking Order

Applies to Wine Trail/Association field, specifically.  What follows is a deliberate, ordered sequence. This order governs two things:

1. **Trail field ordering** — when a venue belongs to more than one trail, the Wine Trail/Association field lists them in pecking order, not in the order they were discovered.
2. **Naming tiebreaker** — when two "Trail" sources name the same venue differently, the venue name from the higher pecking-order Trail source wins or is given higher weight in the tiebreak.

**Current order:**

1. Monticello Wine Trail
2. Nelson 151 Craft Beverage Trail
3. Shenandoah Beerwerks Trail
4. Shenandoah Valley Wine Trail
5. Virginia Spirits Trail
6. Virginia Cider Trail

New trail-type sources, when added, get a deliberate position in this list. See Sources Log below for the distinction between this list and the broader record of everything used to build the venue list.

Adding a new Trail to this list is expected to be extremely rare.  Flag Troy with conspicuous messaging if you are ever headed down the path towards adding a new Trail item to this pecking order.

---

## Same-Venue Resolution

Apparent duplicates show up regularly — same or similar venue name appearing across more than one source. Two categories:

- **Genuinely different physical locations** are not duplicates. A separate tasting room, taproom, or satellite site is a legitimate separate entry, even under the same brand. This is the reason the Data Dictionary has a `Parent Venue` field.
- **Same brand, separate buildings, shared property or address** is a real third category that doesn't resolve mechanically. Two cases this session landed on opposite outcomes, deliberately:
  - **Bold Rock** (Hard Cider / Distillery at The Barrel Barn, both at 1020 Rockfish Valley Hwy., Nellysford) — kept as **two separate rows**, so the spreadsheet has room to capture each building's distinct character.
  - **Devils Backbone** (Brewing / Distilling Co., both on the Roseland campus) — **merged into one row** ("Devil's Backbone Brewing - Basecamp Brewpub & Meadows"), reflecting that the property is one sprawling destination rather than two distinct stops.

Neither decision sets a precedent for the other. Same-address, separate-building situations require a deliberate look each time, not a rule applied automatically.

---

## Scope Discipline

Venue (the name itself) is the one field that's truly mandatory. Everything else is captured opportunistically — take what a source naturally gives, don't chase the other six fields down to completeness once a source has been worked.

This is the current operating posture, not a permanent constraint. It's expected to evolve as sourcing moves from the current narrowly-defined set of trail websites toward a broader, looser approach. Don't read this section as boxing in future sourcing decisions.

---

## Flagged Items Log

A standing ListMaker artifact, separate from the main candidate table. Captures naming ambiguities, same-venue questions, soft geographic calls, and similar open items as they're found — resolved or not. An open flag is a valid permanent state, not a debt that has to be paid down.

---

## Sources Log

A standing artifact, separate from Pecking Order. Tracks every source ever used to build the venue list — trail sites, search queries, future sourcing methods, anything. This list is expected to keep growing as sourcing expands beyond the original six sources (which just so happen to be trail websites.)

**Important distinction:** a new source being added here does not neccessarily mean it'll qualify to be added to the separately-managed Pecking Order.  Adding to pecking order will be a true rarity. Pecking Order is specifically the ordered list of trail-association sources. A future source — a search method, a database, anything outside the trail-association pattern — gets logged in Sources for sure.  Only if the source is a Trail (a curated and official list of venues sharing a theme or geography) would it be considered for adding to Pecking Order.

---

## Explicitly Out of Scope for ListMaker

- **No reconciliation against Andrew's existing data.** ListMaker does not check candidates against the existing master spreadsheet or the Expansion Venues sheet. No dedup-checking at harvest time. Reconciliation against existing data may happen later, separately — undecided whether or when.
- **No formatting standards applied.** Andrew's master-file standards (alphabetical order by Venue, frozen header row, filters enabled, never delete existing data) govern his actual spreadsheet, not ListMaker's output. ListMaker's output is a work queue, not a draft deliverable.
- **No tracking/status column** in the main output table. Queue progress tracking, if needed, lives outside ListMaker's output. (The Flagged Items Log is a different kind of artifact — a decision record, not a workflow-status column — and is not in conflict with this rule.)

---

## How This Feeds the Real Deliverable

The actual Andrew's Driving Service spreadsheet gets built in a **separate chat session**, by orchestrating three ingredients together:
1. ListMaker's output (the venue list)
2. Data Dictionary
3. RowBuilder Operating Guide
