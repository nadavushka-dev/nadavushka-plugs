# Squad Config
Shared by autonomous-squad, tkt-context, tkt-work, tkt-evidence, tkt-ship. Edit per team — the skills refer to these values, rules and status names.

## Rules
- We work in evidence-driven development. **Never skip evidence.**
- Always work with tests. TDD is a must.
- No regressions — run the existing tests.
- We work on multiple tkts at once, so we use git worktrees:
  branch `<fix|feat>/<key>-<n>-<slug>` (lowercase project key, keep the hyphen) under `.worktrees/<key>-<n>-<slug>`.

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
| Name | Tkt Indicator |
| ---- | ------------- |
| Piqk | Piqk |
| Rebuild/Distruptor | rebuild |

Tkt Indicator -> `Project Name`

## Machines Table
| Name | Project |
| ---- | ------- |
| Local | MacOs, Physical machine, For local stuff, not to be used for any of the projects |
| UbCl | Ubuntu, Server, Piqk |
| UbMc | Ubuntu, Server, Piqk + Distruptor |
| Ub3 | Ubuntu, Server, Distruptor |
