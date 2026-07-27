# ListMaker — Decision Log & Rules
*Andrew's Driving Service (ADS) / RowBuilder project*
*Decided in this session. This document is the "logic" output of this session — the actual venue list is generated in a separate session, using these rules plus Data Dictionary v1.2 and the RowBuilder Operating Guide.*

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

**Duplicate policy:**
- Not a duplicate if it's a genuinely different physical location (a separate tasting room, taproom, or satellite site is a legitimate separate entry). This is the reason the Data Dictionary has a `Parent Venue` field.

---

## ListMaker Output Fields (7)

| Field | Why it's in ListMaker's scope |
|---|---|
| Venue | Primary identity field |
| Address | Full offload to RowBuilder — static fact, worth the harvest cost for true determinism (resolves sister-venue ambiguity) |
| City/Town | Static fact, full offload |
| ZIP | Static fact, full offload |
| County | Static fact, full offload; also doubles as the in-scope/out-of-scope check |
| Website | Partial offload — RowBuilder confirms rather than originates from scratch |
| Primary Venue Type | Now a clean, well-defined enum in Data Dictionary v1.2 (Winery/Brewery/Cidery/Meadery/Distillery/Other) — cheap and reliable to capture at harvest time |

**Deliberately excluded from ListMaker's output:**
- **State** — constant across this entire VA-only project, zero disambiguating value
- **Phone** — an attribute, not an identity field; doesn't belong in a primary-key-type list
- **Status** (open/closed) — always needs a fresh check at RowBuilder run time regardless of what's harvested, since staleness between harvest and build is exactly the failure mode this field exists to catch
- **Production Types / Venue Type Enhanced** — deeper classification fields that stay RowBuilder's job, not ListMaker's

---

## Explicitly Out of Scope for ListMaker

- **No reconciliation against Andrew's existing data.** ListMaker does not check candidates against the existing 277-row master or the 881-row "Expansion Venues" sheet. No dedup-checking at harvest time. Reconciliation against existing data may happen later, separately — undecided whether or when.
- **No formatting standards applied.** Andrew's master-file standards (alphabetical order by Venue, frozen header row, filters enabled, never delete existing data) govern his actual spreadsheet, not ListMaker's output. ListMaker's output is a work queue, not a draft deliverable.
- **No tracking/status column.** Queue progress tracking, if needed, lives outside ListMaker's output.

---

## How This Feeds the Real Deliverable

The actual Andrew's Driving Service spreadsheet gets built in a **separate chat session**, by orchestrating three ingredients together:
1. ListMaker's output (the venue list — generated separately, using the rules above)
2. Data Dictionary v1.2
3. RowBuilder Operating Guide
