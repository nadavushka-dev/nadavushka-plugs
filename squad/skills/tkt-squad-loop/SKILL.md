---
name: tkt-squad-loop
description: "Supervisor loop: every <interval> minutes, sweep finished squads, check machine resources, fetch grabbable issue-tracker tickets (e.g. RND-123) and dispatch each to a squad running autonomous-squad on a remote machine, within a seat budget."
argument-hint: "[interval-minutes] [seats] [--hitl] [extra instructions]"
disable-model-invocation: true
---
User input: $ARGUMENTS
- 1st number → `interval` in minutes. Default: 10
- 2nd number → `seats`, the global pool of concurrent squads. Default: 4
- `--hitl` present → pass it to every squad (`squad:autonomous-squad --hitl`). Default: fully autonomous
- Anything else = extra instructions; pass them to every squad verbatim

# Config
**Before anything else:** read `${CLAUDE_SKILL_DIR}/../squad-config.md`

# Requirements
This skill dispatches squads to remote machines, so it needs the `/herdr` skill. Not available → stop and report. Do not fall back to running squads locally.

# Role
You are the supervisor. You never implement, review or touch code. You only fetch, dispatch, sweep and report.

# Definitions
- **Seat** = one dispatched squad (one ticket), however many sessions that squad spawns.
- **Effective seats** = `seats` minus any reduction from Guardrails. Never above `seats`.
- **Ledger** = `.squad/loop-ledger.md` in the current working directory. Create it if missing. It is the source of truth for what you dispatched; never dispatch a ticket that has an open row in it.

  | tkt | project | machine | space | dispatched at | last status | last checked |
  |---|---|---|---|---|---|---|

  A row is open until the Sweep closes it; closed rows move to a `## Closed` section with the reason.

# Start
1. Parse the input and read the config.
2. Run one cycle now.
3. Schedule the remaining cycles every `interval` minutes with the session's recurring scheduler (`/loop <interval>m` or CronCreate), with the prompt: "Run one tkt-squad-loop cycle: re-read <absolute path of this skill file> and follow its Cycle section, args: <original args>". Don't chain one-off wakeups.

# Cycle
Run every step, in order.

## STEP 1 Sweep
For every open ledger row, fetch the ticket status and check the squad's space:

| situation | action |
|---|---|
| status `WFT-QA` (the last status) | close the space, close the row: `done` |
| status is a clarification (`dev clar`, `qa clar`, `prod clar`) | close the space, close the row: `clarification` — the ticket goes back to humans; it's re-grabbable later only if it returns to `todo`/`reopen` |
| status went back to `To Do` / `Reopen` (squad gave up or was reset) | close the space, close the row: `returned` |
| space is gone or its session has exited | close the row: `crashed`; leave a comment on the ticket saying the squad died and at which status |
| open longer than 4h with no status change since dispatch | report it as `stale`; don't kill it. Open longer than 8h → close the space, close the row: `timeout`, comment on the ticket |
| `--hitl` and waiting for human approval | keep it; it still holds a seat; list it in the report |

Update `last status` / `last checked` on rows that stay open.

## STEP 2 Guardrails
On every machine that has an open squad, check free memory and the 1-minute load average.
- **Choking** = free RAM < 15% of total, or load > 1.5 × CPU cores. Do not look at Diskspace.
- Any machine choking → lower effective seats by 1 (minimum 1) and don't dispatch to that machine this cycle.
- Free memory < 5% on a machine → also close the newest squad on it (row: `oom-guard`, comment on the ticket).
- 3 cycles in a row with no machine choking → raise effective seats by 1, up to `seats`.

## STEP 3 Fetch
Free seats = effective seats − open ledger rows. 0 → skip to STEP 5.
Query the tracker for tickets that are:
- in the config's `project key`
- status `To Do` or `Reopen`
- assigned to me (`autonomous-squad` rejects anything else)
- not in an open ledger row

Order: `Reopen` first, then priority (highest first), then oldest created. Take at most the number of free seats.

## STEP 4 Dispatch
For each fetched ticket:
1. **Project.** Look for a `Tkt Indicator` from the Projects Table, case-insensitive, in this order: labels, components, then summary. First match wins. No match or several projects match → skip it and list it in the report as `unroutable`.
2. **Machine.** Candidates = machines in the Machines Table whose Project column names that project (or any `/`-separated part of its name). Never `Local`. Drop machines marked choking this cycle. Pick the one with the fewest open ledger rows; tie → first in the table. No candidate → skip and report `no machine`.
3. **Space.** Create a new herdr space on that machine named `<project-prefix>-<tkt key>-<2-3 word slug>` (lowercase, e.g. `piqk-rnd-123-login-timeout`).
4. **Ledger first.** Write the ledger row *before* starting the squad, so a crash mid-dispatch can't cause a double dispatch.
5. **Start.** In that space run `squad:autonomous-squad <tkt key>` (+ `--hitl`) (+ extra instructions).

## STEP 5 Report
Print one short block per cycle:
- time, effective seats / seats, seats used
- dispatched: tkt → machine/space
- swept: tkt → reason
- skipped: tkt → `unroutable` / `no machine` / no seats
- stale / waiting-for-hitl squads
- guardrail actions taken

use a table format of
| Action | Reason |

Nothing changed → one line: `cycle <time>: no change (<used>/<effective> seats)`.
