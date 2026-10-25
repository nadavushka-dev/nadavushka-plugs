---
name: autonomous-squad
description: "Use when working on an issue-tracker ticket (Jira, Linear, GitHub Issues… e.g. RND-123) end to end — pick up, reproduce or implement, prove with evidence, move statuses. Bugs and features."
argument-hint: "<tkt key> [--hitl] [extra instructions]"
---
User input: $ARGUMENTS
- The ticket key (e.g. RND-123) is in there; take it from that input
- `--hitl` present → pass it to `squad:tkt-ship`. Default: fully autonomous
- Anything else = extra instructions; apply them throughout the run

# Config
**Before anything else:** read `${CLAUDE_SKILL_DIR}/../squad-config.md`

# Squad
If the `/herdr` skill is available, run it. You are the orchestrator/delegator/supervisor — you do not implement, you only delegate with the following instructions.
You may choose how many sessions are in the squad. The minimum is 3 workers:
- Implementor — steps 3–4
- Code Reviewer — the reviewer in step 5. Rejected → back to the Implementor.
- QA — the QA in step 5. Rejected → back to the Implementor.

## Hard Rules using Herder
- Must open squad members in a new tab on the same herdr space you are in.

No herdr → you are the Implementor, and QA/Code Reviewer is a fresh subagent.

# Flow
Run every step, in order. Never skip one.

## STEP 1 Gather TKT Context
Invoke the `squad:tkt-context` skill with the ticket key as args.

## STEP 2 Identify the TKT
- Tkt must be assigned to me — if not, stop and report
- Bug or feature?
- `reopen`? → read QA's comment and the previous PR first
- Move to `In Progress`, create the worktree

## STEP 3 Work
Invoke the `squad:tkt-work` skill with the ticket key (+ extra instructions) as args.

## STEP 4 Prove
Invoke the `squad:tkt-evidence` skill with the ticket key as args.

## STEP 5 Ship
Invoke the `squad:tkt-ship` skill with the ticket key (+ `--hitl` if given) as args.

# Exit rule
Applies to **every** way the run ends, not only STEP 5. Run it before you close your sessions.
- Shipped → `squad:tkt-ship` already released the stack. Verify it (config → Dev Stacks).
- Any other exit (clarification status, stopped, gave up, blocked) → release too:
  1. Commit any uncommitted work as `wip: <key> <what's in it>` and push the branch — releasing deletes the worktree, so unpushed work is lost.
  2. Run the project's `Stack release`, then verify released.
  3. Name the pushed branch in the tkt comment, so a `reopen` resumes from it.
