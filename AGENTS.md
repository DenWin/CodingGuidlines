# Coding Guidelines Agent Manifest

## Context
This repository contains modular YAML policies that define coding guardrails for multiple languages and cross-cutting concerns. AI assistants should rely on this manifest to determine which policy bundles to load, how to apply them, and how to reason about missing guidance.

## Policy File Format
Every policy file uses the same schema so you can load and merge them predictably:
```yaml
version: <SEMVER>
rules:
  - id: <DOMAIN>-<NNN>
    level: MUST|MUST NOT|REQUIRED|SHALL|SHALL NOT|SHOULD|SHOULD NOT|RECOMMENDED|MAY|OPTIONAL
    text: "<Short imperative rule>"
```
Always reference rule identifiers (for example `BASE-001`) when explaining enforcement choices.

## How to Use
1. Read `README.md` for an overview of repository layout and policy composition patterns.
2. Load the `Policies` listed below that match the project context (language, domain, lifecycle stage).
3. Merge rule sets carefully; supplement language-specific guidance with the common baseline.
4. Surface conflicts or gaps instead of guessing; unverifiable assumptions must be confirmed with a human reviewer.
5. Output responses that reference relevant rule identifiers whenever enforcement choices need justification.

## Policy Selection
- Always start with `common/base.yml` and any additional common policies relevant to the task (architecture, performance, documentation, etc.).
- Add the language pack for the project (for example Java, C#, Bash, PowerShell, SQL). Each pack is structured by topic (style, security, testing, architecture, performance).
- Request escalation when required policies appear to be missing. If a policy type is absent, document the risk and continue with best-effort guidance derived from available files.

## Policies
- `common/base.yml` - core engineering expectations applied to every contribution.
- `common/ai-policies.yml` - governance for AI-assisted work, including disclosure and validation duties.
- `common/architecture.yml` - structural design constraints and review touchpoints.
- `common/ci-cd.yml` - build, test, and deployment safeguards.
- `common/observability.yml` - logging, metrics, and tracing conventions.
- `common/performance.yml` - baseline performance practices for any stack.
- `common/documentation.yml` - documentation artefact and traceability requirements.
- `common/compliance.yml` - regulatory and data-handling obligations.
- `common/commit-msg.yml` - commit message structure for traceable history.
- `common/user-stories.yml` - guidance for crafting user stories and acceptance criteria.
- `governance/policy-authoring.yml` - meta-guidance for proposing, reviewing, and documenting policy changes.
- `languages/<language>/<topic>.yml` - language-specific guidance (select the language that matches the current project, e.g. `languages/java/*.yml`).
- `common/ai-todo-intake.md` - backlog intake workflow for repositories that track work in `ToDo.md`.

### Policies for Policy Authors
Follow `governance/policy-authoring.yml` when creating, updating, or retiring policies. Work tracked there defines review workflows, approval requirements, and documentation obligations. Continue storing rationales in the human-facing READMEs to keep prompts lean while preserving context.

## TODO Intake
Follow `common/ai-todo-intake.md` before committing to any backlog item:
- Parse `ToDo.md` by scanning for headings formatted as `### ID: <ID>` and capturing the accompanying fields (`**Title:**`, `**Description:**`, `**Acceptance Criteria:**`, `**Priority:**`, `**Status:**`, `**Notes:**`).
- Treat entries whose `Status` is `Open`, `In Progress`, or `Blocked` as actionable; ignore those marked `Done` unless explicitly requested.
- List the actionable IDs with their titles (include priority when available) and prompt the user to choose work using the `fix <ID> [<ID> ...]` command (example: `fix T-002 T-005`).
- If no selection is provided, pause execution and document the deferral; escalate when `ToDo.md` is missing or malformed.
- Log the intake timestamp, the parsed file path, and the IDs selected before proceeding with any implementation work.

## Contact
Raise issues or proposals via your standard repository workflow. Tag the applicable policy file(s) so reviewers can evaluate the impact surface.


