# Requirements: Loop Visualizer — Phase 1 (Font Readability Only)

**Target file:** `02-html-visualizer/mt_loops_to_html.py`
**Test input:** `samples/0531_05_Sun_results_loops.txt` (mix of loop sizes — exercises both circle diagrams (≤20 trades) and capsule diagrams (21+ trades))

## Context

This is Phase 1 of a two-phase improvement. Phase 1 is intentionally small and low-risk: font size and font choice only. A separate Phase 2 will tackle label collision and truncation — do not attempt that work here. The current script already works correctly; this is a readability polish, not a redesign.

## Current state (for reference)

- Player/username labels: `JetBrains Mono`, font-size `9`, weight `400`
- Game/venue labels: `Cormorant Garamond`, font-size `11`, italic
- Both are too small to comfortably read.

## Requirements

1. **Increase font size** on both player/username labels and game/venue labels within the loop diagrams, enough to noticeably improve readability at normal viewing distance/zoom.

2. **Reconsider font choice** for both label types, prioritizing legibility over stylistic flourish. The current serif italic (game names) and monospace (usernames) pairing can stay conceptually — two distinct type treatments to tell the label types apart — but each should be evaluated for how easy it is to read at small sizes, not just how it looks.

3. **Keep the black background.** Not a problem, do not change it.

## Explicit non-goals (out of scope for Phase 1)

- Do not touch label positioning, offset math, or collision avoidance (the `insideDist`/radial-offset logic on caps, or the rail offset logic on straight sides).
- Do not touch the truncation rule (currently: cut at 32 characters, ellipsis at 30).
- Do not change the capsule/circle diagram shape logic, `capR`, `minStep`, or the circle-vs-capsule trade-count threshold.
- Do not change the interaction model — labels stay visible by default, no click-to-reveal.

## Definition of done

Run the script against `0531_05_Sun_results_loops.txt`. Confirm: both label types are noticeably easier to read than the current version, background is still black, and nothing about layout, spacing, truncation, or interaction behavior has changed.
