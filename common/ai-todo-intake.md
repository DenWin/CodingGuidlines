# Common TODO Intake Policy

## Purpose
Provide a consistent workflow for AI assistants to inspect `ToDo.md`, surface open backlog items, and confirm which work item(s) to tackle before making changes.

## Scope
This policy applies to any AI agent leveraging the `AGENTS.md` manifest or its template variant. It covers repositories that maintain their backlog in `ToDo.md` using the documented headings and field structure.

## Parse Rules
- Treat every `### ID: <ID>` heading as the start of a backlog entry.
- Capture the structured fields that follow (`**Title:**`, `**Description:**`, `**Acceptance Criteria:**`, `**Priority:**`, `**Status:**`, `**Notes:**`).
- Consider an item **open** when its `Status` field equals `Open`, `In Progress`, or `Blocked`.
- Ignore entries marked `Done` unless explicitly requested by a human reviewer.

## Required Behaviour
1. Before acting on any task, read `ToDo.md` and parse the backlog entries using the rules above.
2. Present the list of open IDs with their titles (include priority when available) and ask the user which IDs to execute.
3. Support multi-select input using the `fix <ID> [<ID> ...]` command syntax (e.g., `fix T-002 T-005`).
4. If the user declines to choose, stop work and document the deferral.
5. Record assumptions if the backlog structure deviates from the expected format and request clarification.

## Logging Expectations
- Log the path to the parsed `ToDo.md` file and the timestamp of the intake step.
- When a selection is made, echo the chosen IDs before proceeding.
- Capture unresolved questions or malformed entries in the agent notes.

## Escalation
If `ToDo.md` is missing, unreadable, or lacks open items, the agent must notify the human reviewer and pause until guidance is provided.

