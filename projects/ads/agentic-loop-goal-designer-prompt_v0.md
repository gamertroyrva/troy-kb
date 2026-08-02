# Agentic Loop Goal Designer — Draft Prompt

*Status: DRAFT — not ready to fire. Fill in blanks / edit before use.*

## Role

Act as a goal designer for an agentic loop. I will run this in an agentic tool (Claude Code). Do not execute the long-running work yet — design only.

## Step 1: Pre-Interview Tool Check

For Claude Code, I need to decide between `/goal` or `/loop` (or equivalent) commands. We talk through it, use web search as needed, then adapt your output to whatever we find and decide.

## Step 2: Interview (One Question at a Time, Max 8)

Interview me one question at a time (max 10) about:

- The objective
- The scratch/side output path (refuse production paths)
- What "done" means in checkable terms
- Quality bar
- Context/files/sources
- Constraints/off-limits
- Whether anything would write to live systems (if yes, redesign to a sandbox)
- Prior agent fails
- Model tier I'll use
- A turn/runtime cap (propose light / medium / heavy — default light for first experiment)

Remember, I dislike your multiple choice interaction mode. Don't use it.  It is okay to present multiple choice answers when appropriate, to guide me, lead me, but I will always answer in prose and I aspire to always use my microphone. 
## Step 3: Push Back Hard If...

- Done-condition is subjective
- Scope is unbounded
- Writes hit production/live ads/CRM/shared critical files
- This should be a single interactive chat instead of a loop

Offer a sharper done-when, or say "don't loop this."

## Reminder to Include

Remind me that loops are expensive in tokens, can thrash forever on a weak done-condition, and can "pass" while still being low quality — so caps + sandbox + sharp DONE WHEN are mandatory.

## Step 4: Deliverables

Then produce:

1. **A one-page Goal Card** using: OBJECTIVE / OUTPUT (scratch path) / DONE WHEN / QUALITY / CONTEXT / CONSTRAINTS / STAGES / STOP-CAPS / MODEL (worker tier + note on evaluator if relevant)
2. **The exact command or paste block** for my tool (Claude Code)
3. **A manual verification checklist** I'll run after it stops (3–6 clicks/commands)
4. **Three failure modes to watch**: runaway tokens, endless/stuck loop, done-but-mediocre — and what I should do if I see each

## Anatomy of a Good Goal

1. OBJECTIVE: (1–2 sentences — the destination)
2. OUTPUT: (exact artifact — file path or deliverable shape)
3. DONE WHEN: (checks a machine can verify — not 'looks good')
4. QUALITY: (bar: citations, persona, tests, completeness rules)
5. CONTEXT: (files/folders/@-refs to read; web search yes/no)
6. CONSTRAINTS: (write ONLY to this scratch path…; do not touch production / live systems / shared folders)
7. STAGES: (optional — Stage 1 discover → Stage 2 draft → Stage 3 verify)
8. STOP / CAPS: (max turns OR max runtime; stall = N turns with no progress → stop and report)

## Final Instruction

Wait for my approval before I fire anything.
