---
name: tkt-context
description: "Fetch and read an issue-tracker ticket (e.g. RND-123): fields, comments, attachments. Read-only. Use to discuss or summarize a ticket, or as step 1 of autonomous-squad."
argument-hint: "<tkt key>"
---
Ticket: $ARGUMENTS

# Config
**Before anything else:** read `${CLAUDE_SKILL_DIR}/../squad-config.md`

# Read-only
No status changes, no comments, no assignment.

# Gather
- Fetch the tkt from the **tracker**
- Read everything: fields and comments
- **View every attachment** — download the bytes, sample videos to frames with `ffmpeg`
- Summarize: what's asked, bug or feature, repro steps / acceptance criteria, open questions
