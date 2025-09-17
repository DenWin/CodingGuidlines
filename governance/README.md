# Policy Authoring Rationale

## 1. About This README
This guide explains the meta-policies defined in `governance/policy-authoring.yml`. Use it when evolving the repository’s YAML rules so process expectations stay consistent across teams.

## 2. File-by-File Guidance

### `governance/policy-authoring.yml`
#### GOV-001 — Propose policy changes via tracked issues or pull requests with explicit scope.
- **Rationale:** Routing changes through tracked work items creates an auditable trail and shared visibility.
- **Example:** Open an issue titled "Expand SQL security rules for column-level encryption" before submitting a PR.
#### GOV-002 — Include impacted YAML files and rationale summaries in every policy change description.
- **Rationale:** Explicit references help reviewers understand the blast radius and intent quickly.
- **Example:** In the PR body, list `common/compliance.yml` and `languages/sql/sql-security.yml` with bullet-point rationales for each edit.
#### GOV-003 — Review policy edits with at least one subject matter expert for the affected domain.
- **Rationale:** SMEs provide context on practical implications and ensure rules remain realistic.
- **Example:** Tag a database reliability engineer when adjusting SQL performance policies.
#### GOV-004 — Version policy files using incremental rule IDs and document updates in matching READMEs.
- **Rationale:** Synchronized IDs and READMEs keep machine-readable rules aligned with human explanations.
- **Example:** When adding `SQL-PERF-006`, append the explanation under the SQL performance section in `languages/sql/README.md`.
#### GOV-005 — Validate new or updated policies against sample prompts to ensure clarity and token efficiency.
- **Rationale:** Trial runs reveal ambiguous wording or unnecessary verbosity before rollout.
- **Example:** Paste the updated YAML into a test conversation with the bootstrap prompt to confirm the agent interprets it correctly.

## 3. Applying These Guidelines
Follow these meta-policies whenever introducing, modifying, or retiring rules. They preserve consistency, traceability, and clarity across the policy library.
