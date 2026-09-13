---
name: ledger-tasks-yylo
description: Use the YYLO Ledger to run a Git-native Kanban task board for coding agents, covering task create, list, search, get, mark, update, archive, dependencies, ordering, and archive maintenance, with hash-chained Markdown state stored inside the repository.
category: productivity
agents:
  - claude-code
  - codex
  - cursor
  - opencode
author: yylo-dev
---

# YYLO Ledger task board (ledger-tasks-yylo)

This skill wraps the YYLO CLI (`yy`), a command-line orchestrator for coding agents, repeatable workflows, and receipt-backed repository changes. YYLO Ledger is the independent Git-native record and task store: board state, task history, and mutation receipts live as hash-chained Markdown files in the repository, so every agent and human reads the same task state from Git. Use it when an agent needs a durable Kanban/task ledger instead of ephemeral chat to-do lists.

## When to Use

Use this skill when the user asks to:

- Create, browse, search, or update tasks on a Kanban board ("add a task", "what's ready to work on")
- Manage task status transitions (backlog, todo, in_progress, done) with required response notes
- View or edit task dependencies and blockers, or pick the next unblocked task
- Archive completed tasks, plan immutable cold-archive packs, or inspect task history
- Keep task state inside the Git repository for review and audit
- Coordinate coding-agent work with typed task and lifecycle boundaries

## When NOT to Use

- Do not use for hosted SaaS issue trackers (Jira, Linear, Asana) — use their own skills/integrations instead
- Do not use for one-off edits that need no tracking, or for non-Git directories (the ledger is Git-native)
- Do not edit ledger Markdown files directly; always go through the CLI so hashes and receipts stay valid
- Do not use this skill for agent memory or notes storage; it is a task ledger

## Setup

Install the YYLO CLI (Node.js 20.10 or newer, npm, and Git required):

```bash
npm install --global '@yylo/cli@latest'
yy --version
```

`yy ledger` provides the task surface (the CLI also exposes it as `yy kanban`, a labelled compatibility alias). Verify the installed command surface before relying on it:

```bash
yy ledger --version
yy ledger --help
```

## Workflow

1. Read current state before any mutation: `yy ledger list --limit 10 --sort asc` or `yy ledger search --status todo --tag backend`.
2. Create work when asked: `yy ledger create "Task description" --status backlog --tags feature,backend`.
3. Start only unblocked work: `yy ledger ready` lists tasks whose blockers are done; inspect with `yy ledger deps TASK_ID`.
4. Mark transitions with a required response message: `yy ledger mark in_progress --id TASK_ID --response "Starting work"` and `yy ledger mark done --id TASK_ID --response "Completed: X" --commit <sha>`.
5. Update fields without a status change: `yy ledger update TASK_ID --tags backend,urgent`.
6. Retire finished work: `yy ledger archive TASK_ID`; use `history TASK_ID` for the task ledger, and treat cold-archive packs (`archive-pack plan|create|doctor`) as explicit, owner-authorized maintenance.
7. Prefer commands over file edits, preserve mutation receipts, and never bypass lifecycle state by hand-editing board files.

## Tooling Notes

- `yy ledger get TASK_ID` returns full detail, including dependency and related-task information.
- `deps add --id TASK_ID --blocked-by BLOCKER1 BLOCKER2` wires ordering; `ready` and `order` surface executable work.
- Cross-project routing is opt-in via config and explicit allowed projects; failures never fall back silently.
- The same CLI orchestrates coding-agent runs, typed merge, and release-readiness flows around the task board.

## Examples

**User:** "Add a task for the OAuth retry bug and put it in backlog with the backend tag."

**AI behavior:** Run `yy ledger create "Fix OAuth retry bug" --status backlog --tags backend`, confirm the new task ID, and show the user the created task summary.

**User:** "What's ready to work on right now?"

**AI behavior:** Run `yy ledger ready`, then `yy ledger list --status todo,in_progress --limit 10`; report the unblocked tasks with their IDs, tags, and priority order, and wait for the user to pick one.

**User:** "Mark PROJ-42... I mean task 42 done."

**AI behavior:** Run `yy ledger mark done --id 42 --response "Completed: <what was done>" --commit <sha>` only after verifying the work is committed; otherwise report what remains before marking done.

## Origin

https://github.com/yylo-dev/yylo-skills/tree/main/skills/ledger-tasks-yylo
