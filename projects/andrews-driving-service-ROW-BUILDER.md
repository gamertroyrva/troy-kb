# Andrew's Driving Service — Destination Database
*Handoff Document. Written June 6, 2026. Supersedes the May 17 restart doc.*

---

## The Core Reframe

This is not a spreadsheet cleanup project. This is the construction of critical business infrastructure — a destination database that serves Andrew's driving service across three distinct modes:

- Speed lookup during a live customer phone call
- Structured update sessions — adding venues, refreshing data, marking closures
- A customer-facing itinerary/destination tool on the website (future state — Item 14 in Troy's backlog)

Andrew's existing spreadsheet (CEN_VA_MASTER_CURRENT_1_.xlsx, 280 rows, 97 columns) is not the thing being fixed. It is input data — the raw material to be mined for venue records — and a proof-of-concept vehicle for the row building process.

---

## What Andrew Already Knows

Andrew told Troy directly: the spreadsheet is not the end game. He is not attached to it as the destination. He built it because he needed it and it was the best tool available at the time. The trust gap is not about the spreadsheet structure — it is about whether AI can actually help him solve real problems reliably. One prior bad AI experience made things worse, not better.

Andrew's five original confidence killers (captured May 16):

- Blank cells — incomplete records throughout
- Duplicates — same venue appearing multiple times
- Human comparison finding mistakes and inaccuracies
- Was going to start over one venue at a time (the nuclear option)
- Missing venues — places that exist but aren't on the list

---

## The Architecture Decision

### The Right Answer Long Term

A database — not a spreadsheet. Queryable, structured, enforced by the system not by human discipline, capable of powering a phone lookup tool AND a customer-facing website AND an admin update interface from the same underlying data. Andrew doesn't need to know he has a database. He needs to experience a tool that works.

### The Right Answer Right Now

The proof of concept lives in the spreadsheet. Before any architectural decision is made, the row building process must be validated: can Troy working with Claude populate individual rows of Andrew's 97-column spreadsheet efficiently and with high quality? If yes — great foundation. If it grinds — that becomes the evidence that a redesign is needed sooner.

Do not decide on architecture until the row building proof of concept is complete.

---

## The Immediate Mission

### Row Building Proof of Concept

- Pull up the Andrew spreadsheet chat (file already ingested there)
- Pick one well-known Virginia winery — something easy to research
- Build one row as completely as possible using all available sources
- Note where it flows and where it grinds — this is the data that drives the architecture decision

### Data Sources

- Andrew's existing spreadsheet — already ingested, may contain its own source references
- Two books on Virginia wineries (Andrew mentioned these — titles TBD)
- Monticello Wine Trail resource (web-based)
- Nelson 151 resource (web-based)
- VirginiaWine.org — cited in Andrew's Documentation sheet
- Individual venue websites

Fun destinations (wineries, breweries, cideries, distilleries, meaderies) are the heart of the database. Start there. Functional destinations (airports, medical) matter for revenue but are not where the proof of concept begins.

---

## The Update Cadence Commitment

Troy committed to giving Andrew updates a couple of times a week. This was said. It needs to be honored. A completed proof-of-concept row on Saturday becomes a Saturday update to Andrew. A second update Sunday keeps the momentum visible.

Key framing: an update implies something to update about. Progress must be real, not performative.

---

## Andrew — Who He Is

- Not a spreadsheet power user — built this because he needed it, not because he loves spreadsheets
- Detail-oriented to an impressive degree (97 columns is not an accident)
- 25 years in healthcare — trust, discretion, and attentive care are native to him
- Positions his business around concierge-level service and regional knowledge — the database is what makes that credible at scale
- Had a bad prior AI experience — trust must be earned through results, not promises
- Both he and Troy wake up insanely early — 6am calls are on the table
- Business rhythm is busy / not busy / busy / not busy — scheduling takes coordination but works

---

## Next Session Checklist

- Load standard Troy KB files (about-me.md, anti-ai-writing-style.md, troy-voice-examples.md)
- Load AI Journey Journal and Backlog from Google Drive
- Pull up the Andrew spreadsheet chat for file access
- First conversation: Chat or Cowork? Decide before building anything
- If row building proof of concept is not yet done — that is still the first task
- If proof of concept is done — debrief what it revealed before deciding next step

---

*This document supersedes andrews-driving-service-RESTART.md (May 17, 2026). The earlier doc framed this as a data cleanup project. It is not.*
