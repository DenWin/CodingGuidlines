# Coding Guidelines Repository

## Purpose
This repository provides modular, token-efficient coding policies that can be composed into prompts or manifests for AI-assisted development. Each YAML file captures terse, enforceable rules, while companion READMEs explain context and rationale for human readers.

## Repository Layout
- `AGENTS.md` - primary manifest for AI agents consuming this repository.
- `main_prompt.txt` - bootstrap instruction that forces agents to load the manifest before acting.
- `template/AGENTS.md.template` - copy-ready scaffold for referencing these policies from external projects.
- `common/` - cross-cutting guidance applicable to every stack (base expectations, architecture, CI/CD, observability, performance, documentation, compliance, AI usage, commit hygiene, and user story quality).
- `languages/` - language-specific packs organised by topic (style, security, testing, architecture, performance) for Java, C#, Bash, PowerShell, and SQL.
- `governance/` - meta-policies that dictate how YAML rules are proposed, reviewed, and documented.

## Policy File Format
All policy YAML files share the same schema so agents can compose them consistently:
```yaml
version: <SEMVER>
rules:
  - id: <DOMAIN>-<NNN>
    level: MUST|MUST NOT|REQUIRED|SHALL|SHALL NOT|SHOULD|SHOULD NOT|RECOMMENDED|MAY|OPTIONAL
    text: "<Short imperative rule>"
```
- `version` identifies the schema version for future evolutions.
- Each `id` combines a domain prefix with a numeric sequence (for example `BASE-001`).
- `level` keywords follow RFC 2119 / BCP 14 semantics (MUST, SHOULD, MAY, etc.) so tooling and reviewers interpret requirements consistently.
- `text` holds a terse, actionable instruction; rationales live in the READMEs.

## Creating an External `AGENTS.md`
1. Copy `template/AGENTS.md.template` into the target repository and rename it to `AGENTS.md`.
2. Update the **Project Context** section with repository-specific details (domain, risk posture, dependencies).
3. In the **Relevant Policies** section, list the YAML files required for that project:
   - Always include `common/base.yml`.
   - Add any necessary cross-cutting policies from `common/`.
   - Include only the language packs that match the code in that repository (for example `languages/java/*.yml`).
4. If multiple languages are present, group their policies by component or directory so the agent can load the right bundle per task.
5. Keep the manifest concise; rationale belongs in README files within this repository, not in prompts.

## Glossary
See `docs/glossary.md` for the repository glossary covering all project abbreviations.

## Updating Policies
- Extend existing YAML files when adding rules instead of duplicating topics.
- Update the corresponding README section with rationale for every new rule.
- Follow `governance/policy-authoring.yml` for proposing, reviewing, and documenting any policy changes.

## Feedback
Open an issue or pull request describing the policy gap, the impacted technology, and suggested rule wording. Reference rule identifiers to streamline review.
