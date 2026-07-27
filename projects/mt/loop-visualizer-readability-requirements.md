# Requirements: Loop Visualizer Readability Pass

**Target file:** `02-html-visualizer/mt_loops_to_html.py`
**Test input:** `samples/0531_05_Sun_results_loops.txt` (contains a mix of loop sizes — some 20+, some 18–20, some smaller, so both the capsule shape (21+) and circle shape (20 and below) get exercised)
**Live reference (current, unmodified behavior):** https://playful-genie-7f304d.netlify.app/

## Context

This script generates the HTML visualization of math trade loops that gets shared with trade participants. It already works and already conveys the trade information correctly. None of the items below are showstoppers — the current output is usable as-is. This is a readability/polish pass, not a redesign.

## Requirements

1. **Keep the black background.** This is a preference, not a problem — do not change it.

2. **Increase font size on game/player names within the loop diagrams.** Current size is too small to comfortably read.

3. **Reconsider font choice for readability.** Current font is part of why names are hard to parse at a glance. Prioritize legibility over stylistic flourish.

4. **Fix truncation of lengthy board game titles.** Long titles are currently being cut off. Titles should be readable in full, or truncated in a way that doesn't lose meaning (e.g., ellipsis with enough characters to still identify the game, or a layout that gives long titles more room).

5. **Fix label collisions along the top and bottom semicircles.** Names currently overlap/collide with each other at certain points in the circular/capsule layout. Labels should not visually collide.

## Explicit non-goals

- Do not change the underlying diagram shape logic (capsule for 21+ trades, circle for 20 and below).
- Do not change the interaction model. Names/labels should remain visible by default, the way they are now — not hidden behind a click-to-reveal or selection state.
- Do not restructure the script's architecture beyond what's needed to address the five items above.

## Definition of done

All five items addressed, tested by running the script against `0531_05_Sun_results_loops.txt` and visually confirming: readable font size, readable font choice, no truncated titles losing meaning, no colliding labels — while the diagram still looks and behaves like the current live version in every other respect.
