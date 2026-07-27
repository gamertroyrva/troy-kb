# Restart: Tracking & Encouraging Participation — Agentic Build
*Math Trade Organizer Playbook, Phase 2 — Offer Up Games*
*Session date: Friday, July 3, 2026*

---

## What This Is

During the two-week Offer Up Games phase, Troy manually maintains a Google
Sheet ("Math Trade Tracker") with two sections:

- **Upper section** (4 columns: BGG_Handle, Real_Life_Name, Discord_ID1,
  Discord_ID2) — participants in the *current* math trade, ordered by the
  sequence in which they joined.
- **Lower section** (6 columns: adds Trades_Participated, Avg_Items_Offered)
  — the historical roster of everyone who has ever participated in an RVA
  no-ship math trade, minus anyone already moved up.

This weekend's build target: an agent that keeps this sheet current without
Troy doing the diff-and-edit by hand each time.

Source doc: `playbook-phase2-offer-up-games_v3.html`, Task = "Tracking &
Encouraging Participation."

---

## Agent Scope — What It Does and Does Not Do

**In scope:**
- Diff a fresh list of current-trade participants (BGG handles) against the
  Tracker sheet's upper and lower sections.
- **Existing person joins** (found in lower section) → **true move**. Row is
  deleted from the lower section (no gap left behind), and re-created in the
  upper section using only the 4 shared columns. Trades_Participated and
  Avg_Items_Offered are dropped — they are lower-section-only stats and do
  not travel with a mover.
- **New person joins** (not found anywhere) → new row added to the upper
  section with **BGG_Handle only**. The other three columns are left blank.
- Full autonomy to write to the sheet. No approval gate required.
- Optional: produce a "what I did" summary after each run (nice-to-have, not
  essential — a no-op run would just show up as an empty summary).

**Explicitly out of scope for this agent:**
- Filling in Real_Life_Name or Discord IDs for new participants.
- Any outreach, nudging, or messaging to lower-section people who haven't
  joined yet.
- Any judgment calls beyond "did this handle appear in the current list."

**Match key:** BGG_Handle, exact match. Troy has accepted the risk of this
not holding up perfectly (handle renames, typos) — will fix by hand if/when
it breaks rather than over-engineering fuzzy matching up front.

---

## Two-Phase Build Plan

### Phase 1 — This Weekend (build first)

- **Trigger:** manual. Troy pastes a messy copy of OLWLG's current users
  table into the agent's input.
- **Read:** agent needs to read the Tracker sheet's current state.
- **Write:** agent needs to write moves/adds back to the Tracker sheet.
- **Required infrastructure: Google Sheets API**, read and write. This was
  the single biggest open question resolved this session — see "Key
  Decisions" below.
- **Join order for new adds within a single run:** best-effort based on
  paste cadence (first-seen-in-a-paste). Not exact, but "good enough" per
  Troy, since frequent incremental pulls approximate true join order well
  over the two-week window.

### Phase 2 — Later (after Phase 1 is trusted)

- **Trigger:** swap the manual paste for a server-side fetch against OLWLG's
  stable URL: `https://bgg.activityclub.org/olwlg/mtusers.cgi?listid=380489`
- **Blocker to solve:** this page (like other OLWLG `.cgi` pages) requires
  a logged-in session. Login is BGG-handle + GeekMail magic link, and the
  resulting session cookie persists for months once established.
- **What Phase 2 needs:**
  - One-time extraction of Troy's OLWLG session cookie, stored carefully as
    a secret (not committed to the public GitHub repo in plaintext).
  - A "did I get real data or a login page" check on every fetch, since the
    cookie can silently expire (password reset, OLWLG server migration,
    manual cookie clear) and a naive fetch would fail silently rather than
    loudly.
  - **Explicitly rejected:** browser automation (Tampermonkey-style or
    otherwise) as the fetch mechanism. Troy's words: "I hate brittle browser
    automation... nothing less" than a clean server-side fetch. If
    server-side fetch turns out to be infeasible, the fallback is reverting
    to the Phase 1 manual-paste trigger permanently, not browser automation.
- Sheet-manipulation logic from Phase 1 carries over **unchanged** — only
  the input source changes. Phase 1 is not throwaway scaffolding.

---

## Key Decisions Made This Session

1. **BGG_Handle is the match key**, exact string match, no fuzzy logic.
   Troy is knowingly willing to "skin his knee" on this if a rename or typo
   ever breaks it.

2. **Upper section only carries 4 columns.** History (Trades_Participated,
   Avg_Items_Offered) is lower-section-only data and does not travel on a
   move.

3. **New participants get BGG_Handle only** in the upper section. Identity
   enrichment (real name, Discord IDs) is a human task, not the agent's job.

4. **Full write autonomy, no approval gate.** Rationale: low blast radius —
   even a bad run can be fixed via "start-over reboot," since the sheet is
   reconstructable from the historical roster plus OLWLG's current list.

5. **"What I did" summary is a nice-to-have**, not a blocking requirement.
   A no-op sync (e.g., existing participants just adding more items, no new
   joiners) would naturally show as an empty/no-change report.

6. **Trigger mechanism, in priority order:**
   - Not tied to *how* OLWLG's list gets synced (Tampermonkey or otherwise)
     — the dependency is on the *result* (OLWLG's list being current), not
     the sync mechanism itself.
   - Phase 1: manual paste. Phase 2: server-side fetch with reused session
     cookie. Browser automation was considered and explicitly rejected.

7. **"Poof recreate" (new tab or new file each cycle) was considered and
   rejected as the write mechanism.** Troy's reasoning: he wants to master
   the Sheets API specifically because working through it builds real
   technical capability and confidence — not just because it's the
   theoretically cleaner design. This was a deliberate choice to take the
   harder, more educational path.

8. **A one-time historical join-order reconstruction was completed** for
   the 7 days of this trade that predate any tracking (see below) — this
   data source will not be needed again once the agent is live, since going
   forward, join order falls out naturally from sync cadence.

---

## What Got Done By Hand This Session (Proof of Concept)

Before any agent exists, Troy and Claude manually walked through the full
diff-and-update cycle to validate the design and produce real starting data:

- Reconstructed true join order for the current trade's first 7 days by
  parsing the full 1218-item GeekList export (done in a separate chat) —
  extracting "ships from {handle}" per item, reduced to first-seen-order.
  Result: 32 unique participants in verified join order.
- Applied that order to populate the Tracker's **upper section**: 32 rows,
  4 columns, movers carrying their existing Discord/name data, the 9 brand
  -new-to-the-series joiners left with BGG_Handle only.
- Reduced the **lower section** accordingly: 86 historical rows → 63 (32
  total joiners, minus the 9 who were never in the lower section to begin
  with = 23 actual rows removed).
- Along the way, discovered and resolved a real-world data quality issue:
  Jeff Knapp had two historical handles (ViperMan, TabletopToolbox).
  Consolidated into ViperMan, with a weighted-average recalculation of
  Avg_Items_Offered (76 + 16 items over 5 trades = 18.4 avg). Troy executed
  this merge by hand; historical registry is now 85 people, not 86.
- Ran one live incremental drill: a fresh OLWLG paste surfaced exactly one
  new joiner (drgphx) since the prior sync. Diff-and-move logic held up
  cleanly with no edge cases — the only friction was the manual paste step
  itself, which is precisely what Phase 2 is meant to eliminate.

**Current live state of the Tracker (as of this session):** 33 in the upper
section (32 + drgphx), 62 in the lower section (63 − drgphx), 85 in the
historical registry total.

---

## Format Reminder (now in Claude's memory, but noting here too)

When Claude hands Troy tabular data meant to be pasted into Google Sheets,
it must be tab-separated plain text in a code block — not a markdown table.
Markdown tables paste into a single cell. Markdown tables are still the
right format when the data is meant to be *read* in chat rather than pasted.

---

## Open Threads for the Weekend Build

- [ ] Set up Google Sheets API access (service account or OAuth) — separate
      credential-management task from the eventual OLWLG cookie problem;
      keep them distinct.
- [ ] Build Phase 1 agent: read Tracker state, diff against a pasted OLWLG
      table, execute moves/adds via Sheets API, optionally emit a summary.
- [ ] Once Phase 1 is trusted, tackle Phase 2: session-cookie extraction and
      storage, server-side fetch against `mtusers.cgi?listid=380489`, and
      the login-page-vs-real-data failure check.
- [ ] No rush on Phase 2 — Troy explicitly banked this as a two-part design,
      built in that order, on purpose.
