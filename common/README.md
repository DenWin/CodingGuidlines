<a id="top"></a>
# Common Policy Rationale

## 1. About This README
This document expands on the concise YAML rules inside the `common/` directory. Use it when you need human-readable rationale, illustrative examples, or clarifications that would otherwise bloat AI prompts. Each section maps to a specific policy file and then drills into the individual rules.
Treat the YAML files as the machine-readable source of truth. Use this README to educate humans, onboard new collaborators, or justify decisions during reviews. When adjusting rules, update both the YAML file and the relevant rationale entry to keep them aligned.

## 2. File-by-File Guidance
- [Common Policy Rationale](#common-policy-rationale)
  - [1. About This README](#1-about-this-readme)
  - [2. File-by-File Guidance](#2-file-by-file-guidance)
    - [`common/ai-policies.yml`](#commonai-policiesyml)
      - [AI-001 - Disclose AI-generated contributions in commit messages or review notes.](#ai-001---disclose-ai-generated-contributions-in-commit-messages-or-review-notes)
      - [AI-002 - Validate AI output with automated tests or manual review before submission.](#ai-002---validate-ai-output-with-automated-tests-or-manual-review-before-submission)
      - [AI-003 - Prefer deterministic prompts that reference repository policies explicitly.](#ai-003---prefer-deterministic-prompts-that-reference-repository-policies-explicitly)
      - [AI-004 - Do not allow AI tools to access production data or credentials.](#ai-004---do-not-allow-ai-tools-to-access-production-data-or-credentials)
      - [AI-005 - Capture discovered policy gaps so maintainers can extend the guidelines.](#ai-005---capture-discovered-policy-gaps-so-maintainers-can-extend-the-guidelines)
    - [`common/architecture.yml`](#commonarchitectureyml)
      - [ARCH-001 - Preserve established architectural boundaries and layering constraints.](#arch-001---preserve-established-architectural-boundaries-and-layering-constraints)
      - [ARCH-002 - Document significant architectural decisions in ADRs or equivalent records.](#arch-002---document-significant-architectural-decisions-in-adrs-or-equivalent-records)
      - [ARCH-003 - Design components for testability through clear seams and dependency inversion.](#arch-003---design-components-for-testability-through-clear-seams-and-dependency-inversion)
      - [ARCH-004 - Prefer stateless or idempotent services unless state is explicitly required.](#arch-004---prefer-stateless-or-idempotent-services-unless-state-is-explicitly-required)
      - [ARCH-005 - Align data contracts with published schemas and version them responsibly.](#arch-005---align-data-contracts-with-published-schemas-and-version-them-responsibly)
    - [`common/base.yml`](#commonbaseyml)
      - [BASE-001 - Apply all referenced policies before implementing any change.](#base-001---apply-all-referenced-policies-before-implementing-any-change)
      - [BASE-002 - Structure code so each module has a single, well-defined responsibility.](#base-002---structure-code-so-each-module-has-a-single-well-defined-responsibility)
      - [BASE-003 - Favor readability and clear intent over clever or compact constructs.](#base-003---favor-readability-and-clear-intent-over-clever-or-compact-constructs)
      - [BASE-004 - Validate inputs and fail fast when encountering invalid states.](#base-004---validate-inputs-and-fail-fast-when-encountering-invalid-states)
      - [BASE-005 - Encapsulate side effects behind explicit interfaces.](#base-005---encapsulate-side-effects-behind-explicit-interfaces)
      - [BASE-006 - Leave the codebase cleaner than found by addressing related minor issues.](#base-006---leave-the-codebase-cleaner-than-found-by-addressing-related-minor-issues)
    - [`common/ci-cd.yml`](#commonci-cdyml)
      - [CICD-001 - Keep build pipelines reproducible and runnable without manual steps.](#cicd-001---keep-build-pipelines-reproducible-and-runnable-without-manual-steps)
      - [CICD-002 - Fail the pipeline on test or quality gate regressions without manual overrides.](#cicd-002---fail-the-pipeline-on-test-or-quality-gate-regressions-without-manual-overrides)
      - [CICD-003 - Cache dependencies securely to minimise build times without sacrificing integrity.](#cicd-003---cache-dependencies-securely-to-minimise-build-times-without-sacrificing-integrity)
      - [CICD-004 - Publish build artefacts with immutable version identifiers.](#cicd-004---publish-build-artefacts-with-immutable-version-identifiers)
      - [CICD-005 - Automate security and compliance scans within the deployment workflow.](#cicd-005---automate-security-and-compliance-scans-within-the-deployment-workflow)
    - [`common/commit-msg.yml`](#commoncommit-msgyml)
      - [COMMIT-001 - Separate the subject line from the body with a blank line.](#commit-001---separate-the-subject-line-from-the-body-with-a-blank-line)
      - [COMMIT-002 - Limit the subject line to 50 characters or fewer.](#commit-002---limit-the-subject-line-to-50-characters-or-fewer)
      - [COMMIT-003 - Capitalize the first letter of the subject line.](#commit-003---capitalize-the-first-letter-of-the-subject-line)
      - [COMMIT-004 - Do not end the subject line with a period.](#commit-004---do-not-end-the-subject-line-with-a-period)
      - [COMMIT-005 - Use the imperative mood in the subject line.](#commit-005---use-the-imperative-mood-in-the-subject-line)
      - [COMMIT-006 - Wrap commit body text at roughly 72 characters per line.](#commit-006---wrap-commit-body-text-at-roughly-72-characters-per-line)
      - [COMMIT-007 - Explain what changed and why in the body, omitting low-level implementation detail.](#commit-007---explain-what-changed-and-why-in-the-body-omitting-low-level-implementation-detail)
    - [`common/compliance.yml`](#commoncomplianceyml)
      - [COMP-001 - Classify data handled by the system and apply required safeguards.](#comp-001---classify-data-handled-by-the-system-and-apply-required-safeguards)
      - [COMP-002 - Do not commit secrets or regulated data to source control.](#comp-002---do-not-commit-secrets-or-regulated-data-to-source-control)
      - [COMP-003 - Log access to sensitive operations for auditability.](#comp-003---log-access-to-sensitive-operations-for-auditability)
      - [COMP-004 - Follow jurisdiction-specific retention and deletion policies.](#comp-004---follow-jurisdiction-specific-retention-and-deletion-policies)
      - [COMP-005 - Consult legal or compliance teams before introducing third-party services.](#comp-005---consult-legal-or-compliance-teams-before-introducing-third-party-services)
    - [`common/documentation.yml`](#commondocumentationyml)
      - [DOC-001 - Update documentation alongside code changes that alter behaviour.](#doc-001---update-documentation-alongside-code-changes-that-alter-behaviour)
      - [DOC-002 - Keep README files succinct and link to deeper guides when necessary.](#doc-002---keep-readme-files-succinct-and-link-to-deeper-guides-when-necessary)
      - [DOC-003 - Record rationales and trade-offs in human-readable documents rather than prompts.](#doc-003---record-rationales-and-trade-offs-in-human-readable-documents-rather-than-prompts)
      - [DOC-004 - Use diagrams or tables when they clarify complex flows or data models.](#doc-004---use-diagrams-or-tables-when-they-clarify-complex-flows-or-data-models)
      - [DOC-005 - Ensure onboarding docs stay actionable by revisiting them quarterly.](#doc-005---ensure-onboarding-docs-stay-actionable-by-revisiting-them-quarterly)
      - [DOC-006 - Maintain a repository glossary (for example `docs/glossary.md`) of project abbreviations.](#doc-006---maintain-a-repository-glossary-for-example-docsglossarymd-of-project-abbreviations)
      - [DOC-007 - Keep public or protected APIs documented with up-to-date inline comments (Javadoc, XML doc comments, etc.).](#doc-007---keep-public-or-protected-apis-documented-with-up-to-date-inline-comments-javadoc-xml-doc-comments-etc)
      - [DOC-008 - Provide and maintain feature-level documentation that explains core use cases without restating API details.](#doc-008---provide-and-maintain-feature-level-documentation-that-explains-core-use-cases-without-restating-api-details)
      - [DOC-009 - Keep documentation navigation aids such as tables of contents, indexes, or link hubs up to date.](#doc-009---keep-documentation-navigation-aids-such-as-tables-of-contents-indexes-or-link-hubs-up-to-date)
    - [`common/observability.yml`](#commonobservabilityyml)
      - [OBS-001 - Emit structured logs for every externally observable action.](#obs-001---emit-structured-logs-for-every-externally-observable-action)
      - [OBS-002 - Include correlation identifiers in logs, traces, and metrics.](#obs-002---include-correlation-identifiers-in-logs-traces-and-metrics)
      - [OBS-003 - Protect sensitive data by redacting or hashing before logging.](#obs-003---protect-sensitive-data-by-redacting-or-hashing-before-logging)
      - [OBS-004 - Expose health and readiness probes for services.](#obs-004---expose-health-and-readiness-probes-for-services)
      - [OBS-005 - Define alert thresholds that reflect user impact rather than raw resource usage.](#obs-005---define-alert-thresholds-that-reflect-user-impact-rather-than-raw-resource-usage)
    - [`common/performance.yml`](#commonperformanceyml)
      - [PERF-001 - Establish performance budgets and validate changes against them.](#perf-001---establish-performance-budgets-and-validate-changes-against-them)
      - [PERF-002 - Prefer algorithms with proven complexity bounds over ad-hoc optimisations.](#perf-002---prefer-algorithms-with-proven-complexity-bounds-over-ad-hoc-optimisations)
      - [PERF-003 - Measure before optimising to avoid premature micro-optimisations.](#perf-003---measure-before-optimising-to-avoid-premature-micro-optimisations)
      - [PERF-004 - Design for scalability by avoiding single-threaded bottlenecks.](#perf-004---design-for-scalability-by-avoiding-single-threaded-bottlenecks)
      - [PERF-005 - Use feature flags to roll out performance-sensitive features gradually.](#perf-005---use-feature-flags-to-roll-out-performance-sensitive-features-gradually)
    - [`common/tdd_bdd.yml`](#commontdd_bddyml)
      - [TDD-001 - Write failing tests that describe new behaviour before implementing production code.](#tdd-001---write-failing-tests-that-describe-new-behaviour-before-implementing-production-code)
      - [TDD-002 - Use the smallest test step that expresses the behaviour change clearly.](#tdd-002---use-the-smallest-test-step-that-expresses-the-behaviour-change-clearly)
      - [TDD-003 - Refactor production and test code after the green phase to remove duplication and clarify intent.](#tdd-003---refactor-production-and-test-code-after-the-green-phase-to-remove-duplication-and-clarify-intent)
      - [TDD-004 - Automate behavioural specifications using BDD syntax when collaborating across roles.](#tdd-004---automate-behavioural-specifications-using-bdd-syntax-when-collaborating-across-roles)
      - [TDD-005 - Keep acceptance tests deterministic and independent of external flaky systems.](#tdd-005---keep-acceptance-tests-deterministic-and-independent-of-external-flaky-systems)
    - [`common/user-stories.yml`](#commonuser-storiesyml)
      - [STORIES-001 - Write user stories from the persona perspective with clear outcomes.](#stories-001---write-user-stories-from-the-persona-perspective-with-clear-outcomes)
      - [STORIES-002 - Define acceptance criteria that are testable and measurable.](#stories-002---define-acceptance-criteria-that-are-testable-and-measurable)
      - [STORIES-003 - Prioritise stories based on user value and risk mitigation.](#stories-003---prioritise-stories-based-on-user-value-and-risk-mitigation)
      - [STORIES-004 - Capture non-functional requirements alongside functional ones.](#stories-004---capture-non-functional-requirements-alongside-functional-ones)
      - [STORIES-005 - Link stories to supporting documentation or designs when available.](#stories-005---link-stories-to-supporting-documentation-or-designs-when-available)
  - [3. Applying These Guidelines](#3-applying-these-guidelines)

### `common/ai-policies.yml`
This policy set governs how teams use AI assistance responsibly—covering disclosure, validation, prompting discipline, data protection, and feedback loops.

#### AI-001 - Disclose AI-generated contributions in commit messages or review notes.
- **Rationale:** Transparency helps reviewers scrutinise machine-generated work appropriately.
- **Example:** Add a footer such as "AI-assisted: Code suggestions from Codex" in the pull request description.
[Back to top](#top)

#### AI-002 - Validate AI output with automated tests or manual review before submission.
- **Rationale:** AI suggestions can be syntactically correct but semantically wrong; verification prevents regressions.
- **Example:** Run the project's unit test suite and double-check critical logic before merging an AI-authored patch.
[Back to top](#top)

#### AI-003 - Prefer deterministic prompts that reference repository policies explicitly.
- **Rationale:** Deterministic prompts reduce variability and help the AI stay aligned with standards.
- **Example:** Begin coding sessions by pasting `main_prompt.txt` and the relevant YAML list into the chat to control scope.
[Back to top](#top)

#### AI-004 - Do not allow AI tools to access production data or credentials.
- **Rationale:** Production secrets in AI systems can leak or be logged in unintended places.
- **Example:** Use masked datasets or synthetic fixtures when feeding context into the AI assistant.
[Back to top](#top)

#### AI-005 - Capture discovered policy gaps so maintainers can extend the guidelines.
- **Rationale:** Feedback loops keep policies evolving alongside real workloads.
- **Example:** Open an issue when the AI requires WebSocket guidelines that do not yet exist in the repository.
[Back to top](#top)

### `common/architecture.yml`
These rules protect structural integrity—ensuring architecture decisions remain intentional, testable, and resilient.

#### ARCH-001 - Preserve established architectural boundaries and layering constraints.
- **Rationale:** Layer violations often become brittle coupling that derails deployments.
- **Example:** Prevent UI components from calling database repositories directly; force them through the service layer to maintain the designed flow.
[Back to top](#top)

#### ARCH-002 - Document significant architectural decisions in ADRs or equivalent records.
- **Rationale:** Decision logs capture context for future maintainers and auditors.
- **Example:** Record why a team adopted event sourcing over CRUD in an ADR so successors understand trade-offs without re-litigating them.
[Back to top](#top)

#### ARCH-003 - Design components for testability through clear seams and dependency inversion.
- **Rationale:** Components that accept dependencies via interfaces or constructors are easier to exercise in isolation.
- **Example:** Inject a `PaymentProvider` interface instead of instantiating a concrete gateway directly inside the business object.
[Back to top](#top)

#### ARCH-004 - Prefer stateless or idempotent services unless state is explicitly required.
- **Rationale:** Stateless services scale horizontally and recover from crashes without complicated coordination.
- **Example:** Make an API that recalculates projections each time instead of storing state in memory between requests.
[Back to top](#top)

#### ARCH-005 - Align data contracts with published schemas and version them responsibly.
- **Rationale:** Schema alignment avoids runtime serialization failures between services.
- **Example:** When adding a field to a JSON contract, update the OpenAPI specification and bump the version before releasing clients.
[Back to top](#top)

### `common/base.yml`
The base policy establishes universal engineering expectations that every contribution must respect.

#### BASE-001 - Apply all referenced policies before implementing any change.
- **Rationale:** Loading the full policy set prevents accidental drift from standards that might live outside the current file.
- **Example:** Before touching a Java microservice, collect `common/base.yml`, `common/performance.yml`, and `languages/java/java-performance.yml` so performance guardrails are enforced during design.
[Back to top](#top)

#### BASE-002 - Structure code so each module has a single, well-defined responsibility.
- **Rationale:** Single-responsibility modules are easier to test, replace, and reason about.
- **Example:** Split a class that handles both HTTP parsing and persistence into two collaborators—`RequestParser` and `OrderRepository`—to keep responsibilities explicit.
[Back to top](#top)

#### BASE-003 - Favor readability and clear intent over clever or compact constructs.
- **Rationale:** Highly compressed code slows reviews and hides defects.
- **Example:** Prefer descriptive variables like `totalInvoiceAmount` over chained ternaries or cryptic abbreviations when computing totals.
[Back to top](#top)

#### BASE-004 - Validate inputs and fail fast when encountering invalid states.
- **Rationale:** Early validation prevents bad data from contaminating downstream systems.
- **Example:** Throw an exception when a required configuration value is missing rather than defaulting silently and triggering runtime errors later.
[Back to top](#top)

#### BASE-005 - Encapsulate side effects behind explicit interfaces.
- **Rationale:** Clear seams isolate side effects, making testing and auditing easier.
- **Example:** Route all filesystem writes through a `StorageGateway` interface instead of scattering `File.WriteAllText` calls throughout the codebase.
[Back to top](#top)

#### BASE-006 - Leave the codebase cleaner than found by addressing related minor issues.
- **Rationale:** Opportunistic fixes keep technical debt from accumulating between major refactors.
- **Example:** While updating a function, rename mislabeled variables or remove dead imports touched by the change.
[Back to top](#top)

### `common/ci-cd.yml`
CI/CD policies protect the build, test, and release pipeline so changes are trustworthy and repeatable.

#### CICD-001 - Keep build pipelines reproducible and runnable without manual steps.
- **Rationale:** Reproducible pipelines reduce "works on my machine" issues and support automation.
- **Example:** Store build scripts alongside the code so developers can run `./ci/build.sh` locally with the same inputs as CI.
[Back to top](#top)

#### CICD-002 - Fail the pipeline on test or quality gate regressions without manual overrides.
- **Rationale:** Automatic failure enforces quality standards consistently.
- **Example:** Configure the pipeline to block merges when unit tests fail or coverage drops below the agreed threshold.
[Back to top](#top)

#### CICD-003 - Cache dependencies securely to minimise build times without sacrificing integrity.
- **Rationale:** Caches speed up builds, but must verify checksums to avoid tampering.
- **Example:** Use a package proxy with signature verification rather than downloading dependencies from arbitrary mirrors.
[Back to top](#top)

#### CICD-004 - Publish build artefacts with immutable version identifiers.
- **Rationale:** Immutable artefacts make rollbacks predictable and ensure traceability during incident response.
- **Example:** Tag Docker images with semantic versions (`service:1.4.0`) instead of mutable tags like `latest` for production releases.
[Back to top](#top)

#### CICD-005 - Automate security and compliance scans within the deployment workflow.
- **Rationale:** Embedding scans in CI/CD ensures critical checks run consistently rather than depending on ad-hoc reviews.
- **Example:** Add SAST and dependency vulnerability scans as required stages before deployment jobs trigger.
[Back to top](#top)

### `common/commit-msg.yml`
Commit message rules keep project history consistent, searchable, and useful during reviews or audits.

#### COMMIT-001 - Separate the subject line from the body with a blank line.
- **Rationale:** Git tools rely on the blank line to distinguish metadata from the explanatory body.
- **Example:**
  ```
  Fix checkout bug

  Explain the root cause and the fix here.
  ```
[Back to top](#top)

#### COMMIT-002 - Limit the subject line to 50 characters or fewer.
- **Rationale:** Short subjects fit well in git log, email notifications, and other narrow displays.
- **Example:** "Fix race in payment webhook" conveys the change in under 50 characters.
[Back to top](#top)

#### COMMIT-003 - Capitalize the first letter of the subject line.
- **Rationale:** Capitalization keeps history consistent and matches the tone of release notes.
- **Example:** Use "Add" instead of "add" at the beginning of the subject.
[Back to top](#top)

#### COMMIT-004 - Do not end the subject line with a period.
- **Rationale:** Trailing punctuation wastes characters and looks awkward in summaries.
- **Example:** Prefer "Update docs for OAuth" rather than "Update docs for OAuth.".
[Back to top](#top)

#### COMMIT-005 - Use the imperative mood in the subject line.
- **Rationale:** Imperative phrasing describes what the commit will do when applied.
- **Example:** "Refactor retry logic" reads like an instruction, unlike "Refactored retry logic".
[Back to top](#top)

#### COMMIT-006 - Wrap commit body text at roughly 72 characters per line.
- **Rationale:** Wrapping improves readability in terminals and email clients with fixed widths.
- **Example:** Format explanations as paragraphs with manual breaks near column 72.
[Back to top](#top)

#### COMMIT-007 - Explain what changed and why in the body, omitting low-level implementation detail.
- **Rationale:** Reviewers and future maintainers need context about intent rather than code snippets.
- **Example:** Summarize the defect and the chosen fix, linking to design docs if necessary.
[Back to top](#top)

### `common/compliance.yml`
Compliance guidance keeps the codebase aligned with data classification, retention, and legal obligations.

#### COMP-001 - Classify data handled by the system and apply required safeguards.
- **Rationale:** Data classification determines encryption, access, and retention rules.
- **Example:** Tag records containing PII as "Confidential" and enforce encrypted storage plus restricted access lists.
[Back to top](#top)

#### COMP-002 - Do not commit secrets or regulated data to source control.
- **Rationale:** Version control is not a secure vault; history is difficult to purge.
- **Example:** Store API keys in a secret manager and reference them via environment variables rather than hardcoding.
[Back to top](#top)

#### COMP-003 - Log access to sensitive operations for auditability.
- **Rationale:** Auditors and incident responders rely on access trails to investigate anomalies.
- **Example:** Record when administrators export customer data and include the operator ID plus timestamp.
[Back to top](#top)

#### COMP-004 - Follow jurisdiction-specific retention and deletion policies.
- **Rationale:** Regulations mandate how long different data classes may be stored.
- **Example:** Automatically delete EU customer telemetry after 30 days to comply with GDPR storage limitations.
[Back to top](#top)

#### COMP-005 - Consult legal or compliance teams before introducing third-party services.
- **Rationale:** New services may alter data flows or create contractual obligations.
- **Example:** Engage compliance before adopting a new analytics SaaS to verify it satisfies privacy requirements.
[Back to top](#top)

### `common/documentation.yml`
Documentation rules keep knowledge accessible, current, and proportionate across audience-specific channels.

#### DOC-001 - Update documentation alongside code changes that alter behaviour.
- **Rationale:** Accurate docs keep teams aligned and reduce onboarding friction.
- **Example:** When adding a new API parameter, update the API reference and usage guides in the same pull request.
[Back to top](#top)

#### DOC-002 - Keep README files succinct and link to deeper guides when necessary.
- **Rationale:** Concise entry points help readers find key workflows quickly.
- **Example:** Summarise setup steps in the README and link to `/docs/setup/advanced.md` for niche configurations.
[Back to top](#top)

#### DOC-003 - Record rationales and trade-offs in human-readable documents rather than prompts.
- **Rationale:** Preserving reasoning outside prompts saves tokens and keeps history accessible to everyone.
- **Example:** Capture why a security exception was granted in `SECURITY.md` instead of embedding it in AI instructions.
[Back to top](#top)

#### DOC-004 - Use diagrams or tables when they clarify complex flows or data models.
- **Rationale:** Visual aids communicate relationships faster than prose.
- **Example:** Include a sequence diagram illustrating how services coordinate during checkout to contextualise the text description.
[Back to top](#top)

#### DOC-005 - Ensure onboarding docs stay actionable by revisiting them quarterly.
- **Rationale:** Regular reviews prevent outdated steps from wasting new hire time.
- **Example:** Schedule a quarterly documentation day to validate onboarding instructions against the latest infrastructure.
[Back to top](#top)

#### DOC-006 - Maintain a repository glossary (for example `docs/glossary.md`) of project abbreviations.
- **Rationale:** A dedicated glossary keeps shorthand consistent across teams and avoids misunderstandings.
- **Example:** Store a `docs/glossary.md` file and link to it from the project README.
[Back to top](#top)

#### DOC-007 - Keep public or protected APIs documented with up-to-date inline comments (Javadoc, XML doc comments, etc.).
- **Rationale:** Accurate inline API docs save consumers from diving into implementation details.
- **Example:** Refresh Javadoc or XML summaries when method parameters or behaviour changes.
[Back to top](#top)

#### DOC-008 - Provide and maintain feature-level documentation that explains core use cases without restating API details.
- **Rationale:** Feature docs let stakeholders grasp capabilities and workflows without sifting through low-level references.
- **Example:** Maintain a `docs/features.md` outline describing main flows like checkout, reporting, or onboarding.
[Back to top](#top)

#### DOC-009 - Keep documentation navigation aids such as tables of contents, indexes, or link hubs up to date.
- **Rationale:** Stale navigation erodes trust and makes it harder to discover relevant material.
- **Example:** Refresh the README Table of Contents whenever headings are added, removed, or renamed.
[Back to top](#top)

### `common/observability.yml`
Observability rules ensure telemetry remains trustworthy, privacy-aware, and actionable during incident response.

#### OBS-001 - Emit structured logs for every externally observable action.
- **Rationale:** Structured fields allow effective search, alerting, and correlation.
- **Example:** Log JSON objects with fields like `requestId`, `userId`, and `action` instead of plain strings.
[Back to top](#top)

#### OBS-002 - Include correlation identifiers in logs, traces, and metrics.
- **Rationale:** Correlation IDs tie together activity across microservices during incident triage.
- **Example:** Propagate an `X-Request-ID` header from the API gateway through downstream calls and include it in all telemetry.
[Back to top](#top)

#### OBS-003 - Protect sensitive data by redacting or hashing before logging.
- **Rationale:** Logs often outlive data retention windows; leaking sensitive information in them is a compliance risk.
- **Example:** Replace credit card numbers with hashes or tokens before writing to logs.
[Back to top](#top)

#### OBS-004 - Expose health and readiness probes for services.
- **Rationale:** Probes enable orchestrators to remove unhealthy instances and avoid cascading failures.
- **Example:** Implement `/healthz` and `/readyz` endpoints that check dependencies like databases and downstream APIs.
[Back to top](#top)

#### OBS-005 - Define alert thresholds that reflect user impact rather than raw resource usage.
- **Rationale:** Alerts tied to customer experience reduce noise and highlight real problems.
- **Example:** Trigger an alert when error rates exceed 1% for five minutes instead of when CPU usage briefly spikes.
[Back to top](#top)

### `common/performance.yml`
Performance rules protect budgets, emphasize measurement, and promote sustainable optimisation practices.

#### PERF-001 - Establish performance budgets and validate changes against them.
- **Rationale:** Budgets provide measurable targets to prevent regressions.
- **Example:** Set an SLA of "p95 latency < 500 ms" and run load tests after significant changes to confirm compliance.
[Back to top](#top)

#### PERF-002 - Prefer algorithms with proven complexity bounds over ad-hoc optimisations.
- **Rationale:** Known complexity keeps scaling predictable, avoiding algorithmic surprises in production.
- **Example:** Replace a nested loop that sorts repeatedly with a single pass using a priority queue.
[Back to top](#top)

#### PERF-003 - Measure before optimising to avoid premature micro-optimisations.
- **Rationale:** Metrics highlight genuine hotspots and save time compared to guesswork.
- **Example:** Profile a report generator to confirm slow SQL queries before rewriting business logic.
[Back to top](#top)

#### PERF-004 - Design for scalability by avoiding single-threaded bottlenecks.
- **Rationale:** Bottlenecks constrain throughput even when resources are available.
- **Example:** Parallelise CPU-bound tasks using worker pools rather than a single synchronous loop.
[Back to top](#top)

#### PERF-005 - Use feature flags to roll out performance-sensitive features gradually.
- **Rationale:** Gradual rollouts allow safe observation under real load before full exposure.
- **Example:** Enable a new caching layer for 10% of traffic and monitor metrics before turning it on globally.
[Back to top](#top)

### `common/tdd_bdd.yml`
The TDD/BDD policies shape the red-green-refactor flow and shared language for specifying behaviour.

#### TDD-001 - Write failing tests that describe new behaviour before implementing production code.
- **Rationale:** Starting with a failing test makes the desired behaviour explicit and prevents gold-plating.
- **Example:** Capture the expected JSON response in a unit test before building the controller logic that returns it.
[Back to top](#top)

#### TDD-002 - Use the smallest test step that expresses the behaviour change clearly.
- **Rationale:** Small increments keep feedback fast and isolate the cause of failures.
- **Example:** Add a single assertion for a new edge case instead of rewriting an entire fixture when expanding coverage.
[Back to top](#top)

#### TDD-003 - Refactor production and test code after the green phase to remove duplication and clarify intent.
- **Rationale:** The refactor step preserves code quality and prevents test suites from decaying into brittle scripts.
- **Example:** Extract a helper method shared by multiple tests right after they pass to keep assertions focused.
[Back to top](#top)

#### TDD-004 - Automate behavioural specifications using BDD syntax when collaborating across roles.
- **Rationale:** BDD scenarios provide a shared language for product, testing, and engineering to agree on outcomes.
- **Example:** Write a Gherkin scenario in `Given/When/Then` format that business stakeholders can review before development starts.
[Back to top](#top)

#### TDD-005 - Keep acceptance tests deterministic and independent of external flaky systems.
- **Rationale:** Reliable acceptance suites protect release confidence and avoid false alarms.
- **Example:** Stub third-party payment gateways during acceptance tests so intermittent outages do not block releases.
[Back to top](#top)

### `common/user-stories.yml`
User story policies ensure backlog items stay user-focused, testable, and linked to supporting artefacts.

#### STORIES-001 - Write user stories from the persona perspective with clear outcomes.
- **Rationale:** Persona-driven stories keep delivery focused on user value rather than tasks.
- **Example:** "As a signed-in customer, I want to download invoices so I can reconcile expenses" conveys who benefits and why.
[Back to top](#top)

#### STORIES-002 - Define acceptance criteria that are testable and measurable.
- **Rationale:** Testable criteria allow objective confirmation of completeness.
- **Example:** Add bullet points like "Given a valid invoice, When the customer clicks download, Then a PDF is generated within 3 seconds."
[Back to top](#top)

#### STORIES-003 - Prioritise stories based on user value and risk mitigation.
- **Rationale:** Highest-value or highest-risk work should land first to maximise impact.
- **Example:** Deliver authentication hardening ahead of cosmetic UI tweaks when planning a sprint.
[Back to top](#top)

#### STORIES-004 - Capture non-functional requirements alongside functional ones.
- **Rationale:** Performance, security, and accessibility expectations often determine success.
- **Example:** Append "Must meet WCAG AA contrast requirements" when reworking a dashboard story.
[Back to top](#top)

#### STORIES-005 - Link stories to supporting documentation or designs when available.
- **Rationale:** Traceability aligns teams around shared artefacts and reduces ambiguity.
- **Example:** Attach design mockups and API contracts to the story so developers and testers share the same reference.
[Back to top](#top)

## 3. Applying These Guidelines
Use this README as a navigational companion while the YAML files remain the canonical, machine-readable ruleset.
[Back to top](#top)

