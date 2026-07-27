# Andrew's Driving Service — RowBuilder Handoff
*Written June 6, 2026. Supersedes the earlier June 6 restart doc.*

---

## What Got Built This Session

Planning is complete. RowBuilder is fully architected and ready to execute.

Specifically:
- Data dictionary oriented and verified against all 93 fields
- Blank handling convention established
- Seven research batches defined, named, and populated
- Model allocation decided per field
- Two new columns added to data dictionary: Batch and Model
- Batch and Model values pasted into data dictionary (done in Excel)
- Systems Thinking & Design Decisions document written and saved

---

## RowBuilder Definition

RowBuilder is a Claude-powered structured prompt workflow, operated by Troy, run in Chat. Inputs are a venue name and the data dictionary. Output is a fully populated row ready to paste into Andrew's spreadsheet.

**This is not a Python script. It is a prompt-driven research and population workflow.**

---

## Blank Handling Convention

- **Explicitly excluded fields** (data dictionary says "do not populate") — left blank, no notation
- **Attempted but unavailable** — NOTFOUND

---

## The Eight Batches

| Batch | Name | Model |
|-------|------|-------|
| 1 | 1_VenueID | Sonnet46 |
| 2 | 2_Contact_Location | Sonnet46 |
| 3 | 3_Hours_Access | Sonnet46 |
| 4 | 4_Experience | Sonnet46 |
| 5 | 5_Character | Opus47 |
| 6 | 6_Events_Lodging | Sonnet46 |
| 7 | 7_Policies_Close | Sonnet46 |
| 8 | 8_Do_Not_Populate | N/A — not executed |

**One model per batch. Sonnet46 at Low effort (default). Opus47 at Extra effort, Thinking on (default).**

The Model column in the data dictionary is the A-side baseline of an A/B experiment to be completed in a future session. Andrew wins the early rounds — execute the row, keep good records, run the B-side later.

---

## Session Start Checklist

- [ ] Load standard Troy KB files (about-me.md, anti-ai-writing-style.md, troy-voice-examples.md)
- [ ] Load AI Journey Journal and Backlog from Google Drive
- [ ] Paste this handoff doc
- [ ] Upload the data dictionary (DataDictionary_ADS_Locations.xlsx)
- [ ] Pick the first winery
- [ ] Switch to Sonnet46 Low for Batches 1–4, 6–7
- [ ] Switch to Opus47 Extra for Batch 5
- [ ] Execute batch by batch — do not attempt all 93 fields in one prompt

---

## Winery Selection

Not yet decided. Criteria: well-known Virginia winery, good web presence, enough data available to stress-test the full 93-field process. Decide at session start.

---

## Source Log

The data dictionary field #88 (Source Reference) is out of scope for RowBuilder1.0 — do not populate in the spreadsheet. RowBuilder maintains a **separate source log artifact** outside the spreadsheet, capturing what sources were used for each field during the row build. Create this artifact at the start of the first RowBuilder run.

---

## Open Questions for Andrew

These surfaced during data dictionary work and need an Andrew conversation before they can be resolved:

- **Field #38** Venue Type (Estate/Urban/Farm/Brewpub) — overloaded field, needs an explicit list of acceptable values; consider splitting into two fields
- **Field #39** Beverage Types — discuss whether to break into individual Yes/No fields
- **Fields #40/#50** Tasting Fee overlap — are these intentionally distinct or organic duplication?
- **Fields #37/#71** Tours overlap — same question
- **Fields #51/#70/#77** Food overlap — same question
- **Field #59** Reconciliation Flag — needs definition before it can come into scope
- **Fields #60–#64** META fields (_domain, _canon, _addr, _phone, Notes) — need definitions
- **Andrew Display Order** — when ready, discuss a human-logical reordering of the 93 columns

---

## What Andrew Needs to Know (Confidence Building)

Surface these at the right moment — not all at once:
- The three operational modes the database serves
- The NOTFOUND convention and what it means for data quality visibility
- The open questions listed above
- The future-state vision: Andrew adding a new venue himself

---

## Update Cadence Commitment

Troy committed to giving Andrew updates a couple of times a week. A completed proof-of-concept row is an update to Andrew. Progress must be real, not performative.

---

*Next session: Pick the winery. Build the first row.*
