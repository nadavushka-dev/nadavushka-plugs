---
name: squad-loop
description: "Routine for fetching tkts from tracker and delegate to a squad"
argument-hint: "<interval> <seats> [extra instructions]"
disable-model-invocation: true
---
User input: $ARGUMENTS
- `interval` (in minutes) -> define the time interval for the loop, defaults in 10 minutes
- `seats` -> define the pool size, defaults in 4

# Config
**Before anything else:** read `${CLAUDE_SKILL_DIR}/../squad-config.md`

# Squad
If the `/herdr` skill is available, run it. You are the orchestrator/delegator/supervisor — you do not implement, you only delegate with the following instructions.

# Purpose
You are a delegator running in a loop of <interval>.
Your goal is to fetch tkts from the tracker and delegate them to squad using the `squad:autonomous-squad` skill.
use the `Machines Table` and `Projects Table` to decide on which machine to dispatch the squad.
Each squad will get its own space on the relevant machine, and the space will be renamed to a short descriptive name with a prefix that explains the project.

# SEATS
You will count the seats taken.
You have a pool of `<seats>`.
This is to manage the machine resources and not to get to Out Of Mem.

# Sweep / Cleanup
Every cycle you will check the state of the dispatched tkts. 
If the squad have done (or the tkt status is in its last stage), you will close the squad's space in order to free up machine resources.

# Guardrails
- You will check the machine resources every cycle.
-- If the the machine is choking on resources, you will take action in decreasing the seats number.
