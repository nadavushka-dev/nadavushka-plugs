---
name: tkt-ship
description: "Ship a worked issue-tracker ticket (e.g. RND-123): PR → CI → code review → adversarial QA → evidence on the tkt → merge → cleanup, moving statuses. Use standalone or as step 5 of autonomous-squad."
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
- Code review: the squad's Code Reviewer (or a fresh subagent) reviews the diff — correctness, scope, tests, readability. Rejected → fix, repeat
- QA: the squad's QA (or another fresh subagent — never the reviewer) is adversarial. Re-runs red → green with `squad:tkt-evidence` — never trusts the Implementor's artifacts — then tries to break it: edge cases, bad input, adjacent flows, regressions. Feature → it works; bug → it's fixed. Rejected → fix
- Any fix after code review changes the diff → back through code review, then QA. Both must pass on the same final commit
- Post evidence (before and after) + PR link on the tkt + log time of 1h → `cr`
- `--hitl`? → wait for human approval
- Squash merge to the **default branch** → `WFT-QA`
- Clean up: kill this run's processes, then run the project's `Stack release` (config → Dev Stacks) — it removes the stack, its data and the worktree. Never a bare `git worktree remove`.
- Verify released (config → Dev Stacks). Containers left → you are not done: report the leftover project in the tkt comment and to the orchestrator
