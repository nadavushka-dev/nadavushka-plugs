---
name: tkt-work
description: "Work an issue-tracker ticket (e.g. RND-123): bug → reproduce red, then minimal fix; feature → implement per PRD. Use standalone or as step 3 of autonomous-squad."
argument-hint: "<tkt key> [extra instructions]"
---
User input: $ARGUMENTS

# Config
**Before anything else:** read `${CLAUDE_SKILL_DIR}/../squad-config.md`

# Before you start
- No tkt context in this conversation → invoke the `squad:tkt-context` skill with the ticket key first
- No worktree for this tkt → create it (see Rules)

# Bug?
## Reproduce → red
Reproduce the bug and capture the **red artifact in the bug's medium** (see the `squad:tkt-evidence` skill).
Not screenshot-able is never a reason to stop — switch medium (test / curl / db shell / log).
No red artifact in any medium → `qa clar`.

## Solve
- Minimal changes to fix the bug

# Feature?
- The PRD must be followed
- Ambiguous, contradicts or conflicts with existing features → flag it in a comment, move to `prod clar`
