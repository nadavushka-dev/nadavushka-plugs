---
name: tkt-evidence
description: "Prove a change with red → green evidence (screenshots, curl, failing-then-passing test, DB query, consumer outcome). Use standalone, as step 4 of autonomous-squad, or as QA verifying someone else's work."
argument-hint: "[tkt key] [extra instructions]"
---
User input: $ARGUMENTS

# Config
**Before anything else:** read `${CLAUDE_SKILL_DIR}/../squad-config.md`

# Prove
- Before and after whenever possible. Save every artifact and report its path.
- If one evidence type isn't available, switch to another.
- Verifying someone else's work (QA)? Re-run the red → green yourself — never trust their artifacts.

The evidence must be posted in the tkt comments with the title: "EOP" stands for Evidence Of Proof.

## Evidence Types
| tkt class | red → green proof |
|---|---|
| **UI / visual** | **UI evidence tool** before/after **screenshots** (the current default) |
| **UI vs a design reference** (tkt cites a Figma node, or attaches a reference HTML/PNG as "the design") | screenshots are the red; the green is a **measured table vs the reference rendered at the same viewport** — rect, radius, gaps, centre offset — for the touched element **and its neighbours** |
| **API / contract** | `curl` the endpoint → wrong status/body, then correct (save both) |
| **backend logic / calculation / money-math** | a **unit/integration test at the root cause** that fails first, then passes |
| **data / DB state** | a db query showing the wrong stored state, then correct (+ any migration) |
| **event / Kafka / async** | produce the trigger and capture the **consumer's observable outcome** (dispatcher log row / DB row / outbound call) wrong, then right. A unit test at the producer is a regression guard, **not** this proof |
