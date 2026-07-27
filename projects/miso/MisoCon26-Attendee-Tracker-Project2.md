# MisoCon26 Attendee Tracker — Project Document

*Last updated: May 25, 2026*

---

## Project Goal

When someone purchases a MisoCon26 ticket, their attendee information should automatically appear as a new row in a Google Sheet — one row per attendee, in real time, with no manual effort. That sheet becomes the master attendee tracker for the Misophonia Association to use for convention planning and check-in.

This project was requested by Jaci (Misophonia Association), builds on prior Zap work done by Chad Ingham, and is being prototyped and developed by Troy.

---

## Key People

| Person | Role |
|---|---|
| Jaci | Misophonia Association — project requester, end user of the Google Sheet |
| Chad Ingham | Association tech volunteer — built the original Mailchimp Zap, has admin access to Association Zapier account |
| Troy | Builder — WordPress admin access, personal Zapier account for prototyping |

**Chad's contact:** chad.ingham@gmail.com / WhatsApp  
**Best time for Troy to do sensitive site work:** ~4:30 AM Eastern (low traffic, no real customers shopping)

---

## The Tech Stack

- **WordPress site:** https://misophonia-association.org/
- **Ticketing plugin:** Event Tickets with Tickets Commerce module (by The Events Calendar)
- **Payment gateway:** PayPal (the only active gateway in Tickets Commerce)
- **Automation layer:** Zapier
- **Destination:** Google Sheets (Troy's Google account for prototype)

**Important:** WooCommerce is also installed on the site but is NOT involved in ticket purchases. Tickets Commerce handles the entire purchase flow independently. This distinction caused significant early confusion and dead ends (see Appendix).

---

## Google Sheet — Prototype

**MisoCon26 Attendee Tracker (Prototype)**  
https://docs.google.com/spreadsheets/d/1H-2bZCxxaPi8QFlJVd56uR0Y32CbZEq6LpmX4Nf0Ulc/edit

### Full Column Schema (14 columns from native CSV export)

| # | Column | Status |
|---|---|---|
| 1 | Ticket | ✅ Working |
| 2 | Check In | ✅ Working |
| 3 | Age Range | ❌ Phase 2 |
| 4 | Phone Number | ❌ Phase 2 |
| 5 | Traveling From | ❌ Phase 2 |
| 6 | Accessibility Needs | ❌ Phase 2 |
| 7 | Relationship to Misophonia | ❌ Phase 2 |
| 8 | Order ID | ✅ Working |
| 9 | Order Status | ✅ Working |
| 10 | Ticket ID | ✅ Working |
| 11 | Ticket Holder Name | ✅ Working |
| 12 | Ticket Holder Email Address | ✅ Working |
| 13 | Purchaser Name | ✅ Working |
| 14 | Purchaser Email Address | ✅ Working |

**9 of 14 columns are flowing automatically. 5 remain (Phase 2).**

---

## Zapier Setup — Troy's Account

**Zap name:** Zap2Tix2GoogShee  
**Zapier account:** Troy's personal account (free trial / prototype)  
**Status:** Live and published as of May 25, 2026

### How the Integration Works

Event Tickets has a native Zapier integration — it does NOT use a manually pasted webhook URL. Instead:

1. Inside WordPress (Tickets → Settings → Integrations), you generate a **Consumer ID** and **Consumer Secret** for a named connection
2. In Zapier, you authenticate the Event Tickets app using those credentials plus the site URL
3. Zapier polls the plugin's endpoints on a schedule (every 15 minutes on free plan) asking "any new records since last check?"
4. When new attendees appear, the Zap fires — one run per attendee record

This is a **polling** model, not a push/webhook model. 15-minute lag is acceptable for this use case.

### Trigger Configuration

- **App:** Event Tickets
- **Trigger event:** Attendees endpoint
- **Authentication:** Consumer ID + Consumer Secret generated May 25, 2026 under WordPress user "troyrysedorph"
- **Site URL:** https://misophonia-association.org/

### Action Configuration

- **App:** Google Sheets
- **Action:** Create Spreadsheet Row
- **Sheet:** MisoCon26 Attendee Tracker (Prototype)
- **Mapping:** 9 available payload fields mapped to corresponding columns

### WordPress API Key Location

WordPress Admin → Tickets → Settings → Integrations → (scroll to Zapier section) → API Keys

Troy's key is listed there. Chad's key ("Zapier - Mailchimp") is also visible — do not delete or modify Chad's entry.

---

## The Attendee Meta Mystery (Phase 2 Problem)

The 5 missing fields (age range, phone, traveling from, accessibility needs, relationship to misophonia) are custom registration fields collected at purchase time. They exist in the database — the CSV export from WordPress shows them fully populated.

However, in the Zapier payload, the **Attendee Meta** field arrives **empty** for every attendee record tested. This is the gap to close in Phase 2.

### What We Know
- The data definitely exists in WordPress (confirmed via CSV export)
- Three attendee records were inspected in Zapier — all showed empty Attendee Meta
- The custom fields are part of the Event Tickets Plus / attendee registration feature

### Leading Theories for Phase 2
1. Attendee Meta may require a specific plugin tier or add-on to expose via the Zapier integration
2. The fields may be accessible via a separate Zapier action ("Find Attendees") rather than the trigger payload
3. There may be a WordPress REST API call available that can enrich the record after the trigger fires — a multi-step Zap

### Recommended Next Step for Phase 2
Ask Chad: "In your Mailchimp Zap, did you ever see the custom registration fields (age range, phone, etc.) come through? Or were you only working with the basic order/purchaser fields?" His answer will tell us whether this is a known limitation or something we're missing in our setup.

---

## Chad's Original Zap — For Reference

Chad built a Zap that triggers on ticket purchase and adds an entry to a Mailchimp audience. Two known shortcomings:

1. **Purchaser-level, not attendee-level** — if one person buys 3 tickets, only 1 record is created
2. **Association's Zapier free trial has likely expired** — his Zap may not be running currently

Chad's API key in WordPress is named "Zapier - Mailchimp" under chad.ingham@gmail.com. It last accessed the Event Tickets App on May 15, 2026.

---

## Remaining Steps to Deliver Value to Jaci

### Phase 1 — Complete the Foundation (Near Term)

1. **Verify the live Zap catches a real purchase** — next time someone actually buys a ticket, confirm a row appears in the prototype sheet automatically
2. **Clean up the prototype sheet** — the test records pulled during Zapier setup (Jennifer Donnelly-Glaspell and family) are real attendee data and should be removed from the prototype
3. **Have the requirements conversation with Jaci** — she has expressed interest in additional features beyond the 14 native columns: summary counts, youth flagging, check-in status tracking, etc. Understand her full wish list before committing to Phase 2 scope
4. **Cosmetic/structural review of the Google Sheet** — make sure column headers, formatting, and sheet structure are what Jaci actually wants

### Phase 2 — Solve the Missing Fields

5. **Investigate the Attendee Meta gap** — see leading theories above; start with asking Chad
6. **Build enrichment step if needed** — may require a multi-step Zap or WordPress REST API call to pull custom field data after the trigger fires
7. **Map and wire remaining 5 columns** once the data source is identified

### Phase 3 — Production Handoff

8. **Move from prototype to production** — this means transitioning from Troy's personal Zapier account to the Misophonia Association's Zapier account
9. **Confirm Association Zapier account status** — free trial may have expired; a paid plan may be needed for production (2-minute polling vs. 15-minute)
10. **Generate a new API key** in WordPress under the Association's credentials (not Troy's personal account)
11. **Coordinate with Chad and Jaci** on the production Zap — either rebuild in the Association account or transfer Troy's prototype
12. **Retire the prototype sheet** and point the production Zap at an official Association-owned Google Sheet
13. **Celebrate**

---

## Appendix — Dead Ends and Wrong Turns

Included so the next session doesn't repeat these explorations.

### Dead End 1: WooCommerce Webhooks

**What we tried:** Set up a Zapier "Catch Hook" Zap, got a webhook URL, pasted it into WooCommerce → Settings → Advanced → Webhooks.

**Why it failed:** WooCommerce is not involved in ticket purchases. Tickets Commerce handles the entire checkout independently. The WooCommerce webhook never fired because WooCommerce never saw the transactions.

**Resolution:** Deleted that Zap. Used the native Event Tickets Zapier integration instead.

### Dead End 2: Check Payments

**What we tried:** Enabled "Check payments" in WooCommerce payment settings, hoping it would appear as a $0 test payment option on the checkout page.

**Why it failed:** The checkout page is rendered by Tickets Commerce, not WooCommerce. Tickets Commerce only shows its own configured gateways (PayPal). WooCommerce gateway settings don't affect the ticket checkout at all.

**Resolution:** Irrelevant once we found the native Zapier integration and real test records.

### Dead End 3: Hunting for a Webhook URL Destination

**What we tried:** Spent significant time looking for a place in Tickets Commerce or WordPress to paste a Zapier webhook URL — PayPal settings, Tickets Commerce settings, various plugin screens.

**Why it failed:** The integration model isn't webhook-based (site pushing to Zapier). It's polling-based (Zapier pulling from the site via authenticated API). There is no URL to paste anywhere.

**Resolution:** Once we found Tickets → Settings → Integrations and understood the Consumer ID/Secret model, this confusion resolved completely.

### Dead End 4: Stripe Test Mode

**Briefly considered:** Connecting Stripe (not yet configured on the site) in test mode to enable fake purchases.

**Why we rejected it:** Even if it worked, a Stripe test order would produce a Stripe-flavored payload — not representative of the PayPal payload that real purchases generate. Payload field mapping would be wrong for production.

**Resolution:** Moot — found real attendee data via the native Zapier integration test trigger.

---

*Document maintained by Troy. To be committed to GitHub repo at troy-kb/projects/MisoCon26-Attendee-Tracker-Project.md*
