# ADS RowBuilder Operating Guide
**Version:** 1.0  
**Version Date:** June 6, 2026, 12:00 PM EDT  
**Project:** Andrew's Driving Service — Locations Database  
**Companion Document:** DataDictionary_ADS_Locations.xlsx  

---

## What RowBuilder Is

RowBuilder is a Claude-powered structured prompt workflow, operated by Troy, run in Claude Chat. Given a venue name, Claude researches the venue using web search and populates all applicable fields from the data dictionary. The output is a fully populated row ready to paste into Andrew's spreadsheet.

**This is not a Python script. It is a prompt-driven research and population workflow.**

---

## Session Start Checklist

Before executing a row build, confirm the following are loaded:

- [ ] Standard Troy KB files (about-me.md, anti-ai-writing-style.md, troy-voice-examples.md)
- [ ] AI Journey Journal and Backlog from Google Drive
- [ ] This Operating Guide
- [ ] DataDictionary_ADS_Locations.xlsx
- [ ] Venue selected and named

---

## Run in Batches, Bitches

RowBuilder 1.0 executes in batches. Each batch is a separate prompt. Do not attempt to populate all 93 fields in one prompt.

| Batch | Name               | Fields | Model      | Effort                    |
| ----- | ------------------ | ------ | ---------- | ------------------------- |
| 1     | 1_VenueID          | 1–12   | Sonnet 4.6 | Default                   |
| 2     | 2_Contact_Location | 13–27  | Sonnet 4.6 | Default                   |
| 3     | 3_Hours_Access     | 28–37  | Sonnet 4.6 | Default                   |
| 4     | 4_Experience       | 38–56  | Sonnet 4.6 | Default                   |
| 5     | 5_Character        | 57–62  | Opus 4.7   | Extra effort, Thinking on |
| 6     | 6_Events_Lodging   | 63–69  | Sonnet 4.6 | Default                   |
| 7     | 7_Policies_Close   | 70–78  | Sonnet 4.6 | Default                   |
| 8     | 8_Do_Not_Populate  | 79–93  | N/A        | Not executed              |

Troy manages model switching manually between batches. Batch 5 requires switching to Opus 4.7 before issuing the prompt, then switching back to Sonnet 4.6 for Batch 6.

---

## Blank Handling Convention

Three states exist. Use exactly the syntax shown.

| State    | Syntax                      | When to Use                                                                                                   |
| -------- | --------------------------- | ------------------------------------------------------------------------------------------------------------- |
| Blank    | *(leave empty)*             | Batch 8 explicitly excluded fields only — fields where the data dictionary instruction says "do not populate" |
| NOTFOUND | `NOTFOUND — [brief reason]` | Attempted, genuinely unavailable — information was sought but could not be found in any sourced listing       |
| N/A      | `N/A — [brief reason]`      | Not applicable to this venue — the field concept does not apply (e.g. no parent entity, no on-site lodging)   |

**Brief reason is required for both NOTFOUND and N/A.** One short phrase — not a sentence, not a white paper. The reason makes the value useful to Andrew in a future conversation and distinguishes it from a skipped field.

**Examples:**
- `NOTFOUND — no TikTok presence found`
- `NOTFOUND — flight price not published on site or any authoritative source`
- `N/A — no parent entity`
- `N/A — no on-site lodging; nearby options include Keswick Hall (~6 miles) and Charlottesville area hotels`

---

## Source Log Convention

Maintain a running source log alongside the row build. The source log:

- Is a flat numbered list
- Maps to the **venue (row)**, not to individual fields or batches
- Grows throughout the session — add a new source each time a new one is consulted
- Is produced as a separate artifact at the end of the session, alongside the full row output
- Corresponds to data dictionary Field #88 (Source Reference), which is out of scope for the spreadsheet in RowBuilder 1.0 but captured here for future use

---

## Timestamp Convention

Fields 77 (Last Verified/Updated) and 78 (Last Verified/Updated ET) are populated at completion of Batch 7.

- Format: `Month Day, Year, HH:00 AM/PM EDT` (or EST in winter)
- Round to the **nearest hour** — do not use approximate times like "approximately 10:30"
- Both fields receive the same value (Field 78 makes the Eastern Time designation explicit; they are not different)
- Eastern Daylight Time (EDT, UTC-4) is in effect approximately March–November; Eastern Standard Time (EST, UTC-5) applies otherwise

---

## Output Format

**During the run:** Each batch is presented as a field-by-field table with field number, field name, and populated value. Flag any notable decisions, ambiguities, or open questions inline after the table.

**At the end:** Produce two outputs:
1. The complete row — all 78 populated fields in a single table, in field number order
2. The source log — flat numbered list of all sources consulted

Troy handles transposing the row for readability if desired — RowBuilder produces the row-oriented version by default.

---

## Winery Selection Criteria

When selecting a venue for a proof-of-concept or first run, favor: well-known Virginia winery, strong web presence, enough publicly available data to stress-test all 93 fields.

---

## Known Open Items (as of v1.0)

- Fields 77 and 78 appear redundant — same value, same definition. Flag to Andrew.
- Field 66 (Shuttle/Transport) definition updated in data dictionary Concerns field to clarify dual purpose: (a) venue-provided shuttle for guests, and (b) bus/large vehicle parking for operators like Andrew.
- A future version of RowBuilder may remove the batch structure entirely for a simpler single-prompt execution. When that happens, increment the version number and update this guide.

---

*This document is the operational complement to DataDictionary_ADS_Locations.xlsx. Together they are everything Claude needs to execute a RowBuilder session.*
