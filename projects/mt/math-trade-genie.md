# Math Trade Genie
*Project note for Troy's Obsidian vault. Attach to Cowork sessions for instant orientation.*
*Last updated: May 23, 2026*

---

## What It Is

A repeatable Python pipeline that processes math trade results from OLWLG into clean, useful outputs. Built block by block in Claude Cowork. The goal: take the raw output files that OLWLG produces and turn them into something beautiful and trustworthy.

---

## Where It Lives

**Code:** `C:\workspaces\math-trade-genie\` (not yet moved here — currently at `Documents/Troy's/AI/Claude Cowork/PROJECTS/MT_Results_Genie/` — migration is deferred, on the backlog)

**Folder structure:**
```
MT_Results_Genie/
├── block1/
│   ├── [Block 1 Python script]
│   └── outputs/
│       └── [unique named .txt files, one per run]
├── block2/
│   ├── [Block 2 Python script]
│   └── outputs/
│       └── [unique named .html files, one per run]
└── block3/
    ├── [Block 3 Python script — in progress]
    └── outputs/
        └── [unique named anomaly report files, one per run]
```

**Input files** (OLWLG export pairs — two .txt files per run) live separately in Troy's Math Trade folder on his computer. Not in the Cowork project folder. Eventually will be drag-and-dropped to kick off a run.

---

## The Blocks

### Block 1 ✅ — Loop Parser
- **Input:** Raw OLWLG output files (pair of .txt files)
- **Output:** Clean, human-readable trade loops .txt with unique filename per run
- **Status:** Complete and battle-tested

### Block 2 ✅ — Visualizer
- **Input:** Block 1 output .txt
- **Output:** Beautiful self-contained .html visualization with unique filename per run
- **Status:** Complete. Has known cosmetic issues to remedy before wide distribution (backlog item 5)
- **Distribution method:** Host on Google Drive with "anyone with link" permissions → share the link. Netlify account also exists for hosted web solution down the road.

### Block 3 🔧 — Anomaly Detector
- **Input:** Block 1 output .txt (loops file)
- **Output:** Plain .txt anomaly report — organizer-only tool, functional not pretty
- **Mechanism:** For each trade edge, estimates board game values via BGG marketplace (primary), Amazon/eBay (secondary), Claude API as gap-filler. Computes value delta between offered and received game. Surfaces Top N largest value jumps (N is configurable, default 10).
- **Status:** In progress. Known limitations:
  - Batching needed for real-world trade sizes (445+ games breaks without it) — backlog item 2
  - Bundle/sweetener detection not yet implemented — backlog item 3. A GeekList item title may say "Everdell" but the description says "Everdell plus all expansions plus $20 cash." Block 3 currently can't see that.
  - Output filename contains "error" — needs rename to "anomaly" — backlog item 1
- **Naming note:** It's an *anomaly* detector, not an error detector. It flags unusual value jumps for human review — it's not declaring anything definitively wrong.
- **Testing data:** Real historical math trade file pairs where known errors occurred exist. Gold-standard validation data. Troy knows where to find these.

---

## Domain Vocabulary

**Math Trade** — A multi-party trade where everyone lists what they have and what they want. An algorithm finds optimal trade loops.

**Trade Loop** — Person A gives to B, B gives to C, C gives back to A. Everyone gets something they wanted. The magic of math trades.

**GeekList Item** — The atomic unit of a math trade. Conversationally called "games" but can be a bundle with sweeteners (additional games, cash, promos) described in the item description — NOT the title.

**Trade Matrix** — Each participant's stipulation of what games they'd accept in return for each game they're offering. Errors in the trade matrix are what Block 3 is designed to surface.

**Sweeteners / Hidden Bundle** — When a GeekList item title undersells the real-world value. Known limitation of Block 3 MVP.

**OLWLG** — The platform used to manage math trades. Authenticated sessions last ~6 months.

**BGG API** — Powerful but now gated. Requires app registration. On the road map.

**Practice Run / Final Run** — Troy runs each math trade approximately 3-4 practice runs plus one final run. The pipeline needs to handle all of these efficiently — orchestration is the next big unlock.

---

## Road Map (beyond Block 3)

- **Orchestration** — Give the Genie an orchestrator so that running all 4-5 runs through the full pipeline is fast and repeatable. Urgent: RVA Math Trade coming around May 25 with ~4 practice runs + 1 final. (Backlog item 4)
- **Block 2 cosmetic fixes** — Before wide distribution / wow-ing groups (Backlog item 5)
- **Hosted web solution** — Table of contents page where all RVA math trade visualizations live. Imagine going to one page and visiting all the visualizations. (Backlog item 6)
- **Bundle/sweetener detection** — Backlog item 3
- **BGG API integration** — Learning item; requires app registration
- **AI-Assisted Math Trade System** — The big vision. Full force multiplier. (Backlog item 7)

---

## Key Decisions Made

- Unique filenames per run (not fixed filenames) — correct design, proliferation is acceptable
- Input files stay in Math Trade folder, not in project folder — clean separation
- Block 3 is organizer-only — plain functional output, no need to make it pretty
- "Anomaly detector" not "error detector" — naming matters; it flags for human review
- Blocks are symmetrical in structure — each has working files folder + outputs subfolder

---

## How to Start a Cowork Session

1. Attach this file as context
2. State which block or road map item you're working on
3. Provide the relevant input files if needed
4. Start a new Cowork task for each discrete scope of work

---

*Source of truth: this file in troy-kb. Update after significant sessions.*
