# Math Trade Organizer Playbook

## Phase 2: Offer Up Games

This is the phase where the math trade stops being an idea and becomes a thing people can act on. For a local no-ship trade, this phase runs about two weeks — and that length isn't arbitrary. It's the runway that lets the rest of this phase do its job.

---

### GeekList Creation and Launch

The Saturday morning this phase begins is the real kickoff. Up to this point, everything has been buzz and anticipation — now there's a concrete artifact people can add games to.

**The mechanics:**

Don't start from a blank GeekList. Clone the prior trade's GeekList and bring its description over — the long block of rules and guidelines at the top is mostly reusable, and editing it from scratch is rarely necessary. From there:

- Update the timing information in **two places**: human-readable at the top of the description (for people reading it), and machine-readable at the bottom (for OLWLG's parsing).
- Create the GeekList as **Public** from the start — not something to toggle later. A private GeekList can't receive additions from people's BGG collections, which defeats the purpose before it begins.
- When registering the GeekList with OLWLG (the CGI step that lets sophisticated users add directly from their BGG collections), the zip code field should reflect the **swap venue's location**, not your home address — for local trades, this keeps form data consistent and meaningful from one trade to the next.
- Once the list has a critical mass of entries (roughly 25+ games), take it out of Draft mode and publish.

**Standing up the Discord section:**

Math Trade RVA is the year-round clubhouse — a standing Discord server that gets a new section of channels for each trade. The pattern repeats: what-where-when, updates, q-and-a, make-a-wish. Creating each channel is genuinely tedious — visit the same-named channel from the prior trade's section, copy its description, create the new channel, save, then edit and paste the description back in. Unlovable, but worth it. The server is one of the two biggest levers this phase has for pulling people in.

---

### Monitoring Additions

This sub-task is really one habit that does double duty, several times a day.

**The visit:** Check BGG for subscription notifications on the GeekList. See what's been added — sometimes that's a genuine "ooh, someone dropped Nucleum into the trade" moment, sometimes it's spotting an entry that's incomplete or doesn't meet the rules. While there, sync the GeekList to OLWLG.

**Why the sync matters — and why it's not optional:** The GeekList itself is a rough way to browse a growing trade — 25-item pagination makes it painful once the list gets large. OLWLG's synced view is what serious participants actually use: sortable, viewable as a single page. Many participants build their want lists incrementally over the two-week period rather than all at once at the deadline — which means they're depending on that synced view being current. Frequent synchronization isn't an organizer convenience; it's keeping the lights on for the tool the community relies on. (This is also the task behind the Tampermonkey script running hourly syncs — automation that's already proven itself.)

**The supervisory layer:** Medium weight — not grave, not trivial. While browsing, watch for GeekList entries that are insufficient or violate the rules, and keep half an eye on whether posters are responding to clarifying questions in the comments ("what version is this, is this the Kickstarter?"). If nobody answers, that may need a nudge later.

**The community layer:** This phase has a steady hum of Discord banter — "can't believe someone dropped Nucleum," "Tim K just joined and added 123 games," "where's Alex?" It's part entertainment, part presence, and part passive signal-reading (is that big drop legit? has someone gone quiet who shouldn't have?).

---

### Encouraging Participation

This isn't a discrete task with its own steps — it's closer to an ongoing condition that the two-week Offers period exists to enable.

**Why two weeks:** Shipping math trades culturally run a one-week Offers period — that community is already assembled, and its participants don't want things dragged out. Local no-ship trades benefit from the extra week specifically *because* the community isn't fully assembled yet. Someone joins on day 3, tells a friend, and that friend joins on day 11. The runway is what makes room for organic growth.

**The Participant Tracker:** Each trade has a Math Trade Tracker Google Sheet. During Offers, the relevant tab tracks participants — yes, this duplicates what OLWLG's user list already shows, but the duplication has a purpose. The sheet is organized with active-trade participants in an upper section and prior participants (organized roughly by trade — most recent trade, the one before that, and so on) in lower sections. As someone joins the current trade, their row gets moved from a lower section up to the active section.

The tedious part is real: scanning the lower sections, finding someone who's joined, cutting and pasting their row. But this scan **is** the outreach-targeting mechanism — comparing who's active against who's been active before surfaces exactly the people worth a personal reach-out.

**Personal outreach is a hallmark of this organization strategy.** The runway gives people time; the personal reach-out is what actually uses that time. Someone joins Discord and goes quiet for a week? That gets noticed, and gets a message.

**A second life for the Tracker:** The sheet's columns capture three identities per person — BGG handle, real name, and Discord handle — because Discord-as-clubhouse means people are known by three different names in three different contexts. This same mapping gets published to the Discord server ahead of the Swap Phase, and feeds the name tags handed out at the swap event itself (real name + BGG handle). The tedious tracker built during Offers pays dividends twice.

---

### Advertising and Awareness — The Big Three, Plus the Long Tail

Three channels do the heavy lifting:

1. **Richmond, VA Board Games (Facebook group)** — the primary channel for reaching local people who aren't yet part of the math trade ecosystem.
2. **Math Trade RVA Discord server** — retains and reactivates people who already know the scene, whether they're returning regulars or people who joined Discord between trades on a friend's recommendation.
3. **GeekMail to past participants** — fired once the GeekList is live and there's something to point people at. This is a newer technique (learned from a Pacific Northwest organizer, first used on the Eastern USA trade) — one data point so far, worth treating as promising rather than proven.

**A judgment call worth preserving:** Posting to BGG's broader Math Trade Announcement Thread used to feel automatic. It isn't anymore. That thread's audience is international, and for a local no-ship trade, the number of people who actually join from that exposure is low — while the support burden from out-of-area people who do join can be disproportionate. (A New Jersey participant, and a couple from Northern Virginia, each required an elevated amount of personal attention.) Whether to post there is now a conditional decision, not a default.

**The long tail** — channels that matter but have lived in memory rather than on paper until now:

- RVA Board Gaming (Facebook group)
- Unplugged Discord Server
- The Kallax Discord Server
- VA Creepers Discord Server
- RiVirCon Discord Server
- Early Morning Gamers Discord Server
- In-person: attending meetups and talking it up. QR codes have been used here too — one linking to the Discord server invite, another to a short "ease-in guide."

This list is a living one. Making it visible is the point — it can keep growing.

---

### A Note on Scope

Everything in this section reflects the full version of how this trade gets organized — including the significant discretionary layer added by running a year-round Discord community. Most math trades don't have a Discord server at all; the majority use a BGG discussion thread, which also means no third identity (Discord handle) to track. The Discord server is the single biggest driver of the extra work described above.

A leaner, more approachable version of this Playbook — aimed at someone deciding whether organizing a trade is within reach — is a future document, not a rewrite of this one. It would largely be this section with everything Discord-dependent removed.
