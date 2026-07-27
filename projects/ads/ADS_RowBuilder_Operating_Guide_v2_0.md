# ADS RowBuilder Operating Guide
**Version:** 2.0  
**Version Date:** June 20, 2026  
**Project:** Andrew's Driving Service — Locations Database  
**Companion Document:** DataDictionary_ADS_Locations (current version)  

---

## What RowBuilder Is

RowBuilder is a Claude-powered structured prompt workflow, operated by Troy, run in Claude Chat. Given a venue name, Claude researches the venue using web search and populates all applicable fields from the data dictionary. The output is a fully populated row ready to paste into Andrew's spreadsheet.

**This is not a Python script. It is a prompt-driven research and population workflow.**

---

## Session Start Checklist

Before executing a row build, confirm the following are loaded:

- [ ] Standard Troy session startup files
- [ ] This Operating Guide
- [ ] Current version of DataDictionary_ADS_Locations spreadsheet
- [ ] Venues about to be worked on are known

---

## Run in Batches, Bitches

RowBuilder executes in batches. Each batch is a separate prompt. Do not attempt to populate all 72 fields in one prompt.

| Batch | Name               | Fields | Model      | Effort                    |
| ----- | ------------------ | ------ | ---------- | ------------------------- |
| 1     | 1_VenueID          | 1–10   | Sonnet 4.6 | Default                   |
| 2     | 2_Contact_Location | 11–25  | Sonnet 4.6 | Default                   |
| 3     | 3_Hours_Access     | 26–35  | Sonnet 4.6 | Default                   |
| 4     | 4_Experience       | 36–50  | Sonnet 4.6 | Default                   |
| 5     | 5_Character        | 51–56  | Opus 4.7   | Extra effort, Thinking on |
| 6     | 6_Events_Lodging   | 57–62  | Sonnet 4.6 | Default                   |
| 7     | 7_Policies_Close   | 63–72  | Sonnet 4.6 | Default                   |

Troy manages model switching manually between batches. Batch 5 requires switching to Opus 4.7 before issuing the prompt, then switching back to Sonnet 4.6 for Batch 6.

---

## Blank Handling Convention

Three states exist. Use exactly the syntax shown.

| State    | Syntax                      | When to Use                                                                                                   |
| -------- | ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| Blank    | *(leave empty)*              | No field in the current data dictionary is designated "do not populate" — this state is retained in case a future dictionary revision adds excluded fields |
| NOTFOUND | `NOTFOUND — [brief reason]` | Attempted, genuinely unavailable — information was sought but could not be found in any sourced listing                                 |
| N/A      | `N/A — [brief reason]`      | Not applicable to this venue — the field concept does not apply (e.g. no parent entity, no on-site lodging)                              |

**Brief reason is required for both NOTFOUND and N/A.** One short phrase — not a sentence, not a white paper. The reason makes the value useful to Andrew in a future conversation and distinguishes it from a skipped field.

**Examples:**
- `NOTFOUND — no TikTok presence found`
- `NOTFOUND — flight price not published on site or any authoritative source`
- `N/A — no parent entity`
- `N/A — no on-site lodging; nearby options include Keswick Hall (~6 miles) and Charlottesville area hotels`

---

## Source References Convention

Field 72 (Source References) carries the running list of sources for the venue's row. It is a regular field within the row — there is no separate source log artifact and no separate end-of-session deliverable for sourcing.

- Grows throughout the session — add a new source to the running list each time a new one is consulted
- Within the chat output, sources are joined with a pipe delimiter: `source1 | source2 | source3`
- When transcribing into the spreadsheet: paste the full pipe-delimited string into the cell as one piece first — this avoids the row-splitting risk that comes from pasting literal line breaks into a cell
- Then convert delimiters to true in-cell line breaks for readability: Find & Replace (Ctrl+H), search `|`, and in the Replace field enter Ctrl+J (inserts a line-break character — shows nothing visibly), then Replace All. Google Sheets equivalent: `=SUBSTITUTE(A1,"|",CHAR(10))` in a helper cell, then paste values back.
- If a better transcription method turns up in practice, update this section.

---

## Timestamp Convention

Fields 70 (Last Verified/Updated) and 71 (Last Verified/Updated ET) are populated at completion of Batch 7.

- Format: `Month Day, Year, HH:00 AM/PM EDT` (or EST in winter)
- Round to the **nearest hour** — do not use approximate times like "approximately 10:30"
- Both fields receive the same value (Field 71 makes the Eastern Time designation explicit; they are not different)
- Eastern Daylight Time (EDT, UTC-4) is in effect approximately March–November; Eastern Standard Time (EST, UTC-5) applies otherwise

---

## Output Format

**During the run:** Each batch is presented as a field-by-field table with field number, field name, and populated value. Flag any notable decisions, ambiguities, or open questions inline after the table.

**At the end:** Produce the complete row — all 72 populated fields in a single table, in field number order. Field 72 (Source References) is populated alongside the rest; there is no separate source log output.

Troy handles transposing the row for readability if desired — RowBuilder produces the row-oriented version by default.

---

## Known Open Items (as of v2.0)

- Fields 63–69 show apparent redundancy across the pet/family-friendly group (Pet Friendly / Pet-Friendly / Pets Policy (Details); Kid Friendly / Kids Policy (Details) / Family-Friendly / Family Friendly) — flag to Andrew for possible consolidation.

---

*This document is the operational complement to DataDictionary_ADS_Locations (current version). Together they are everything Claude needs to execute a RowBuilder session.*
