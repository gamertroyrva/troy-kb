# MisoCon26 Attendee Tracker — Project Restart Document
*Last updated: May 17, 2026*

---

## What This Project Is

An agentic automation that watches for new MisoCon26 ticket purchases and automatically appends a row to a Google Sheet for each attendee — without any human involvement.

This is Troy's return to agent development after Day 1 of his AI learning journey. It is also a real deliverable for the Misophonia Association.

---

## Why It Matters

Jaci Hoffman (Executive Director, Misophonia Association) needs a running list of MisoCon26 attendees as registrations come in. The manual alternative — maintaining a spreadsheet by hand with a drip flow of tedious effort — is an abomination. The agentic alternative is the right answer and a superb intermediate-level AI challenge.

---

## The Stack

- **WordPress** website with **Events Plus** and **Tickets** plugins
- **WooCommerce** for payment processing
- **Zapier** as the integration/automation platform (confirmed by both Nate Johnson and Chad Ingham independently)
- **Google Sheets** as the destination tracker

---

## Key People

| Person | Role |
|--------|------|
| Jaci Hoffman | Executive Director, Misophonia Association. Stakeholder. Accessible for requirements conversations. |
| Chad Ingham | Software engineer volunteer. Built the existing WooCommerce → MailChimp Zap. Troy's technical resource for the existing stack. |
| Nate Johnson | Incumbent WordPress/WooCommerce admin. Predicted Zapier would be required (correctly). |
| Troy Rysedorph | Builder. Owner of the prototype. |

---

## What Chad Confirmed (May 16 Call)

- The existing Zap **is webhook-based** ✅ — not poll-based. This is the right trigger type.
- Chad provided credentials to the **Misophonia Association's Zapier account** for reference.
- The Misophonia Association's Zapier **free trial has expired**. Their Zap may or may not still be functioning.
- Chad could not confirm precisely what fields are in the WooCommerce webhook payload — this is a **TBD to discover** when building.

---

## The Critical Architectural Insight

The existing Chad/Misophonia Zap operates at the **purchaser level** — only the person who buys the tickets gets added to MailChimp. Additional attendees (e.g. a spouse, a child) are NOT captured.

Troy's prototype must operate at the **attendee level** — one row per attendee ticket, not one row per order. A family purchasing 3 tickets = 3 rows.

This is the core problem to solve and the reason Troy is building fresh rather than extending Chad's work.

---

## Prototype Strategy

**Troy builds his own prototype in his own Zapier account.** Chad's Zap remains untouched in the Misophonia Association account.

- Troy's prototype: attendee-level, Troy's Google account, Troy's Zapier account
- Chad's Zap: purchaser-level, Misophonia Association accounts (reference only)
- **Merge happens later**, when prototype is proven and ready for production

This keeps production safe, gives Troy full ownership of the build, and preserves Chad's work as a reference implementation.

---

## The Prototype Google Sheet

Already created in Troy's Google Drive:

**MisoCon26 Attendee Tracker (Prototype)**
https://docs.google.com/spreadsheets/d/1H-2bZCxxaPi8QFlJVd56uR0Y32CbZEq6LpmX4Nf0Ulc/edit

**Column schema** (native CSV export order):

| # | Column |
|---|--------|
| 1 | Ticket |
| 2 | Check In |
| 3 | Age Range |
| 4 | Phone Number |
| 5 | Traveling From |
| 6 | Accessibility Needs |
| 7 | Relationship to Misophonia |
| 8 | Order ID |
| 9 | Order Status |
| 10 | Ticket ID |
| 11 | Ticket Holder Name |
| 12 | Ticket Holder Email Address |
| 13 | Purchaser Name |
| 14 | Purchaser Email Address |

---

## Key Unknown: WooCommerce Payload Contents

The most important thing to discover when building: **what fields does the WooCommerce webhook payload actually contain?**

Specifically: are attendee-level details (name, email, age range, accessibility needs, etc.) nested inside the order as line item metadata — or does the agent need to make a follow-up API call to the WordPress/Tickets plugin to retrieve them?

This determines whether the Zap can populate all 14 columns from the webhook alone, or whether enrichment is needed.

**How to discover this:** Create a Zapier account, set up a WooCommerce webhook trigger, fire a test purchase, and inspect the raw payload.

---

## Next Steps (When Restarting)

1. Create Troy's personal Zapier account
2. Set up a new Zap with WooCommerce webhook as trigger (on order completed)
3. Fire a test purchase and inspect the full payload
4. Map available fields to the 14 Google Sheet columns
5. Determine if attendee-level data is in the payload or requires enrichment
6. Build the append-row action to the prototype Google Sheet
7. Test end-to-end with a real or simulated purchase
8. Celebrate

---

## Requirements Conversation with Jaci

Not yet had. Can happen before, during, or after prototyping. Prototype success definition is **native CSV fields only** — Jaci's additional requirements (summary counts, youth flagging, check-in status, etc.) layer on top of a working prototype.

---

## Zapier Account Notes

- **Misophonia Association account**: Chad has credentials. Troy has access for reference. Free trial expired — may need a paid plan for production.
- **Troy's prototype account**: To be created. Entry-level plan is fine for prototype purposes.
- **Platform decision**: Zapier is the right call here. It's already embedded in the stack, Chad knows it, and the webhook → Google Sheets path is well-documented.

---

*"Troy does what he says he's going to do."*
*Project status: On the back burner. Ready to restart.*
