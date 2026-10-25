# Squad Config
Shared by autonomous-squad, tkt-context, tkt-work, tkt-evidence, tkt-ship, tkt-squad-loop. Edit per team — the skills refer to these values, rules and status names.

## Rules
- We work in evidence-driven development. **Never skip evidence.**
- Always work with tests. TDD is a must.
- No regressions — run the existing tests.
- We work on multiple tkts at once, so we use git worktrees:
  branch `<fix|feat>/<key>-<n>-<slug>` (lowercase project key, keep the hyphen) under `.worktrees/<key>-<n>-<slug>`.
- A squad owns every dev stack it starts. A tkt that leaves the squad — shipped, clarification, stopped, gave up — leaves **no running containers** behind.
  Never `git worktree remove` on its own: it deletes the stack's config and orphans the running stack. Always use the project's `Stack release` (see Dev Stacks).

## Values
| key | value |
|---|---|
| tracker | Jira (REST API, not MCP) |
| project key | `RND` |
| default branch | `main` |
| UI evidence tool | Playwright |

## Handle TKT Status
| status | alias | when |
|---|---|---|
| `To Do` | `todo` | for grabs — new tkt from product/QA |
| `Reopen` | `reopen` | for grabs — QA judged a previous fix as not done |
| `In Progress` | | tkt picked up |
| `Dev Clarification` | `dev clar` | a developer decision is needed, at any time |
| `QA Clarification` | `qa clar` | bug ambiguous or not reproducing |
| `Product Clarification` | `prod clar` | feature ambiguous, contradicting or conflicting |
| `Code Review` | `cr` | evidence posted + PR opened |
| `Waiting for Deployment to QA Environment` | `WFT-QA` | merged — after human approval if `--hitl` |

Every move to a clarification status comes with a tkt comment stating the exact question.

## Projects Table
| Name | Project field value | Tkt Indicator (fallback) | Calude Alias Command |
| ---- | ------------------- | ------------------------ | -------------------- |
| Piqk | PiQK | Piqk | cldp |
| Rebuild/Distruptor | Rebuild | rebuild | cldd |

Tkt Indicator -> `Project Name`

## Dev Stacks
Per-tkt compose stacks a squad brings up in its worktree. `<project>` = the compose project name; `<key>` = lowercase tkt key (e.g. `rnd-123`).

| Project | Stack name pattern | Stack release (stack + data + worktree) | Stack stop (keep data) |
| ------- | ------------------ | --------------------------------------- | ---------------------- |
| Rebuild/Distruptor | `disruptor-feat-<key>-*`, `disruptor-fix-<key>-*` | from the main checkout: `./.claude/skills/dev-env/cleanup-worktree.sh <worktree path> --delete-branch` | `docker compose -p <project> --profile "*" stop` |
| Piqk | `piqk-feat-<key>-*`, `piqk-fix-<key>-*` | `docker compose -p <project> down -v --remove-orphans`, then `git worktree remove <worktree path>` | `docker compose -p <project> stop` |

- Only these patterns are squad stacks. Anything else (`disruptor-dev`, the always-on `piqk-*` infra: redis, kafka, mongo, infisical, mailhog…) is **never** stopped or removed by a squad or the loop.
- Released = `docker ps -a --filter label=com.docker.compose.project=<project>` prints nothing. Check it; don't assume.

## Machines Table
| Name | Project |
| ---- | ------- |
| Local | MacOs, Physical machine, For local stuff, not to be used for any of the projects |
| UbCl | Ubuntu, Server, Piqk |
| UbMc | Ubuntu, Server, Piqk + Distruptor |
| Ub3 | Ubuntu, Server, Distruptor |
