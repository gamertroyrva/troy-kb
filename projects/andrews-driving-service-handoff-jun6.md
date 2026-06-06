# Andrew's Driving Service — Destination Database
*Handoff Document. Written June 6, 2026. Supersedes the earlier June 6 restart doc.*

---

## The Core Reframe

This is not a spreadsheet cleanup project. This is the construction of critical business infrastructure — a destination database that serves Andrew's driving service across three distinct modes:

- Speed lookup during a live customer phone call
- Structured update sessions — adding venues, refreshing data, marking closures
- A customer-facing itinerary/destination tool on the website (future state — Item 14 in Troy's backlog)

---

## What Got Built Today (June 6)

A full data dictionary for Andrew's 93-column Venues spreadsheet. This is the foundation that makes RowBuilder possible.

Upload the data dictionary at session start — it is the system of record for all field-level decisions including definitions, row builder instructions, redundant fields, and issues for Andrew.

---

## The Immediate Mission — RowBuilder

The data dictionary is complete enough to begin building RowBuilder. The proof of concept goal:

- Pick one well-known Virginia winery
- Build one complete row using the data dictionary as the guide
- Note where it flows and where it grinds
- Walk away with evidence about repeatability across ~250 venues

### Data Sources Available
- Andrew's spreadsheet (already ingested in the Andrew spreadsheet chat)
- Monticello Wine Trail (web)
- Nelson 151 (web)
- VirginiaWine.org
- Individual venue websites
- Andrew's two Virginia winery books (titles TBD)

---

## Next Session Checklist

- Load standard Troy KB files (about-me.md, anti-ai-writing-style.md, troy-voice-examples.md)
- Load AI Journey Journal and Backlog from Google Drive
- Paste this handoff doc
- Upload the data dictionary
- Decide: Chat or Cowork for RowBuilder development?
- Pick the first winery
- Build the first row

---

## The Update Cadence Commitment

Troy committed to giving Andrew updates a couple of times a week. A completed proof-of-concept row becomes an update to Andrew. Progress must be real, not performative.

---

## Andrew — Who He Is

- Not a spreadsheet power user — built this because he needed it
- Detail-oriented to an impressive degree (93 columns is not an accident)
- 25 years in healthcare — trust, discretion, and attentive care are native to him
- Had a bad prior AI experience — trust must be earned through results, not promises
- Both he and Troy wake up insanely early — 6am calls are on the table

---

*Written June 6, 2026. Next session: RowBuilder proof of concept.*
