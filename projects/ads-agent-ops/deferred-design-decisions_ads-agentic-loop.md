# Deferred Design Decisions — ADS Agentic Loop (Backlog Item 13)
*Companion document to the Issues Log. The Issues Log captures breadth — quick entries as memory triggers. This document captures depth — the full reasoning behind a deliberate "good enough for now" choice, so the "why" isn't lost to a one-line row.*

*A deferred decision here is not a mistake and not an oversight. It's a conscious tradeoff: the better path is known, a lesser path is chosen anyway, and a specific trigger condition is named for when to revisit it.*

---

## Decision #1 — Worker's tool access for writing to the Master

**Date:** August 7, 2026 (surfaced during rung 1 attempt #1 postmortem, August 6)

**The better way:**
A narrow, purpose-built write mechanism — either a scoped script worker can invoke only with specific, constrained arguments (e.g. slug + field + value), or splitting file-write capability out of worker entirely into a separate, dumber, non-reasoning component. Either version means worker's actual power to mutate the Master is exactly as wide as intended by design, enforced by what the mechanism itself permits — not trusting the subagent prompt alone to stay in bounds.

**The chosen way, for now:**
Grant worker `Bash` directly, so it can drive Python/openpyxl itself to read and write `data/ads_master.xlsx`.

**Why this is responsible enough for now (Troy's words):**
"I simply don't think the grant worker bash is going to result in irreversible harm. For now, I am watching."

The operative condition is *attended* operation — Troy present, watching the run, able to intervene the moment something looks wrong, exactly as happened during the rung 1 attempt that surfaced this decision. Blast radius is low at this stage: low row counts, a known-good git history under the Master, and Troy's own external archiving as a second recovery layer. The priority right now is proving the three-agent architecture can function at all — getting to real agentic work — not hardening a piece of it against a threat model that doesn't fully apply yet at this scale and this level of supervision.

**What triggers revisiting this:**
Not a specific rung number — that's deliberately left open. The trigger is a **state change, not a row count**: the first run where Troy is not watching. Troy's own framing: *"I do aspire to build in a guardrail where this decision is revisited before launching into a 'human walks away from computer' rung run... I don't know yet which rung this is yet. Just know that walking away from the computer IS THE PROJECT."*

In other words — every rung up through whichever one is still attended can reasonably run on this deferred decision as-is. The first rung where Troy plans to be asleep or away is the hard gate: worker's Bash-driven write access to the Master gets reassessed (Option B or C from the original discussion) before that specific run fires, not after.

**Status:** Open. Revisit at the attended → unattended transition, whenever that rung is identified.

---

## Decision #2 — Scope of the `Bash(git push *)` deny rule

**Date:** August 13, 2026 (repeated pattern — first surfaced Issues Log Entry 20, August 10; recurred live during an advisory session on this same date)

**The better way:**
A deny rule scoped to only the three autonomous subagents (worker, business-analyst, qa) — the actual actors this rule exists to restrain during an unattended `/goal` run — leaving the orchestrator itself and any human-directed session free to push normally via Bash.

**The chosen way, for now:**
Keep the blanket `Bash(git push *)` deny rule exactly as it is, session-wide, with no distinction between an autonomous subagent's Bash call and the orchestrator's or a human's. It has now blocked a legitimate, non-subagent push twice (Entry 20, and again during this session) — not a hypothetical cost. The accepted workaround: use the GitHub API tools (`create_or_update_file`, `push_files`, `create_branch`, `create_pull_request`) instead of Bash `git push` whenever a real push is needed outside the autonomous loop.

**Why this is responsible enough for now:**
Claude Code's current permission system does not appear to support scoping an allow/deny rule to a specific subagent's invocations — `settings.json` rules apply to the whole session, full stop, regardless of which subagent's tool call triggered them. Given that, the real choice isn't "broad rule vs. narrow rule" — it's "broad rule vs. no rule." And a real risk sits behind removing it: Deferred Decision #1 already grants worker unscoped `Bash` access, so nothing besides this deny rule stands between a worker subagent going off-script and it successfully pushing to remote mid-run. Losing that backstop to fix a workflow inconvenience — an extra step through the GitHub API instead of the git CLI — would trade a real guardrail for a minor convenience. The friction is real, but it's now bounded, understood, and has a documented workaround, rather than being a silent trap.

**What triggers revisiting this:**
Two independent triggers, either one sufficient:
- Claude Code ships genuine per-subagent-scoped permissions (rules that apply only to specific Task/subagent invocations, not the whole session) — migrate this rule to that mechanism the moment it's available.
- The attended → unattended transition that triggers Decision #1's reassessment. If worker's own Bash grant gets narrowed at that point (Option B or C from Decision #1), this deny rule's whole reason for existing may become moot — worth reassessing both together, not separately.

**Status:** Open.

---

## Decision #3 — Whether to harden WebFetch-blocked-domain handling (virginiawine.org, and blocked domains generally)

**Date:** August 16, 2026 (observed during Rung 5, August 15)

**The better way:**
Pre-route virginiawine.org straight to WebSearch (skip the doomed direct WebFetch entirely), and add a general Wayback Machine fallback to Empty Fetch Discipline for any domain that blocks direct WebFetch. Fully designed, drafted, and verified working during this cycle — a real archived snapshot was checked by hand and found to contain substantive, structured data. The complete implementation exists in **PR #7** (`andrews-driving-services`), closed without merging rather than deleted, so the actual code is there to revive if this decision ever gets revisited.

**The chosen way, for now:**
Leave current behavior as-is. Worker and business-analyst still attempt the doomed direct WebFetch to virginiawine.org (absorbing one wasted attempt per affected venue), and no Wayback Machine fallback exists yet for any blocked domain.

**Why this is responsible enough for now (Troy's words):**
"The overall process is not showing signs of degradation from the existence of these two issues." The diversity of information available on the internet more than compensates for a small number of sources that intentionally wall themselves off from AI-fueled harvesters. Rather than complicate the infrastructure now in pursuit of a marginal improvement, live with it.

**What triggers revisiting this:**
The process actually showing signs of degradation attributable to these two issues — for example, a venue where the *only* available information sits behind a WebFetch-blocked domain with no usable snippet, producing an avoidable NOTFOUND that a fix would have closed, or the wasted-attempt cost becoming noticeable at higher row counts.

**Status:** Open.

---

## Decision #4 — Whether to constrain business-analyst's resolution effort on lower-stakes forks

**Date:** August 16, 2026 (observed during Rung 5, August 15)

**The better way:**
Give business-analyst a lighter-weight resolution path for forks where "close enough, known-to-fluctuate" is an acceptable answer (pricing being the clear example) — distinct from forks requiring the kind of exhaustive, multi-source verification appropriate for something like jurisdiction or county. Right now business-analyst applies the same high-effort research posture to every fork, regardless of what's actually at stake.

**The chosen way, for now:**
Leave business-analyst's resolution behavior exactly as-is. No effort-tiering, no changes to its research posture for any fork type.

**Why this is responsible enough for now (Troy's words):**
"The current quest is to harden the infrastructure to set myself up for a supervised, unattended run or runs. That's the priority for today." Tuning business-analyst's effort calibration is a real improvement opportunity, but it isn't what today's hardening work is for, and touching it now risks pulling focus from the actual priority. "My spidey sense says don't mess with this. I'm okay FOR NOW if business-analyst agent overinvests in resolving issues that in reality are not terribly important."

**What triggers revisiting this:**
A future run showing a notable departure from the established ~7–8%/row cost baseline, combined with a high volume of business-analyst escalations in that run's Audit Log — especially if the numerical impact is severe. Troy won't have full visibility into how much effort any single escalation consumed, but the Audit Log will show which items needed escalating, and his own per-run statistics capture (turns, wall-clock, block % per row) will surface any run where cost and escalation volume together suggest the overinvestment is actually costing something real, not just a theoretical concern.

**Status:** Open.

---

*Add future deferred decisions below, same format: the better way / the chosen way / why it's responsible for now / what triggers revisiting it.*
