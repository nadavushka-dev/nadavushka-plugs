---
name: tkt-ship
description: "Ship a worked issue-tracker ticket (e.g. RND-123): PR → CI → independent review → evidence on the tkt → merge → cleanup, moving statuses. Use standalone or as step 5 of autonomous-squad."
argument-hint: "<tkt key> [--hitl] [extra instructions]"
disable-model-invocation: false
---
User input: $ARGUMENTS
- `--hitl` (human in the loop) → wait for human approval before merging. Default: fully autonomous.

# Config
**Before anything else:** read `${CLAUDE_SKILL_DIR}/../squad-config.md`

# Before you start
- No red → green evidence for this tkt yet → invoke the `squad:tkt-evidence` skill first

# Ship
- Open PR → CI must pass (fails → fix, repeat)
- Review: a fresh reviewer (the squad's QA/Code Reviewer, or a fresh subagent) re-runs the evidence with `squad:tkt-evidence` and reviews the diff. Rejected → fix, repeat
- Post evidence (before and after) + PR link on the tkt → `cr`
- `--hitl`? → wait for human approval
- Merge to the **default branch** → `WFT-QA`
- Clean up: kill this run's processes, remove the worktree
