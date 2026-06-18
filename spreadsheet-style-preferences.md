# Spreadsheet Style Preferences
*Universal formatting preferences for any spreadsheet Claude builds for Troy. Attach alongside about-me.md when the work involves Excel/Sheets output, or fold into the standing context set permanently.*

---

## Justification

- **Horizontal:** left-justify everything — headers and data — except columns holding sequence/numeral identifiers (e.g., Phase #, Task #, Step #), which are center-justified, headers and data both.
- A new identifier/label column (e.g., a concatenated display label) defaults to left-justified until usage reveals otherwise.
- **Vertical:** center-justify cell content vertically throughout the spreadsheet.

## Header Row

- Bold, black text.
- Gray background — mid-tone: not so light it's unnoticeable, not so dark it turns ugly or compromises contrast with the black text.
- Always frozen.

## Column Freezing

- Do not assume or guess at column freezing. Leave columns unfrozen by default. Troy will handle column freezing manually on the rare occasion it's warranted.

## Gridlines

- Gridlines on all spreadsheet content.
- Include one trailing row, gridlined but genuinely empty (no formulas, no inherited formatting) — a visual cue that more rows can be added.

## Row Height

- For medium-to-large, text-heavy spreadsheets: use automatic row height (rows size to content), not a uniform/fixed height.

## Formulas

- Prefer live formulas over hardcoded values wherever a cell's content is derived from other cells (e.g., a label concatenating ID columns) — so the sheet stays correct if inputs change.

## General

- Consistent, professional font (e.g., Arial) — standard baseline absent other instruction.
- Zero formula errors in any delivered workbook.

---

*Created: June 2026, during Automation Opportunity Matrix buildout.*
