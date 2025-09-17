<a id="top"></a>
# SQL Policy Rationale

## 1. About This README
This README supplements the SQL policy YAML files, providing narrative explanations and practical examples for architecture, performance, security, style, and testing rules. Use it when clarifying decisions with teammates or documenting why a guideline exists.
Treat the YAML files as the machine-readable source of truth. Use this README to educate humans, onboard new collaborators, or justify decisions during reviews. When adjusting rules, update both the YAML file and the relevant rationale entry to keep them aligned.

## 2. File-by-File Guidance
- [`languages/sql/sql-architecture.yml`](#languagessqlsql-architectureyml)
  - [SQL-ARCH-001 - Apply `../../common/architecture.yml` principles before database-specific architecture decisions.](#sql-arch-001---apply-commonarchitectureyml-principles-before-database-specific-architecture-decisions)
  - [SQL-ARCH-002 - Normalise schemas to the agreed level unless denormalisation is justified.](#sql-arch-002---normalise-schemas-to-the-agreed-level-unless-denormalisation-is-justified)
  - [SQL-ARCH-003 - Design primary keys as stable, unique identifiers and avoid natural keys when volatile.](#sql-arch-003---design-primary-keys-as-stable-unique-identifiers-and-avoid-natural-keys-when-volatile)
  - [SQL-ARCH-004 - Version database schema changes using migrations tracked in source control.](#sql-arch-004---version-database-schema-changes-using-migrations-tracked-in-source-control)
  - [SQL-ARCH-005 - Define indexes based on workload analysis and maintain them proactively.](#sql-arch-005---define-indexes-based-on-workload-analysis-and-maintain-them-proactively)
  - [SQL-ARCH-006 - Separate OLTP and analytics workloads to prevent resource contention.](#sql-arch-006---separate-oltp-and-analytics-workloads-to-prevent-resource-contention)
- [`languages/sql/sql-performance.yml`](#languagessqlsql-performanceyml)
  - [SQL-PERF-001 - Apply `../../common/performance.yml` budgets and practices before database-specific optimisations.](#sql-perf-001---apply-commonperformanceyml-budgets-and-practices-before-database-specific-optimisations)
  - [SQL-PERF-002 - Review query execution plans for regressions before release.](#sql-perf-002---review-query-execution-plans-for-regressions-before-release)
  - [SQL-PERF-003 - Use set-based operations instead of row-by-row processing when possible.](#sql-perf-003---use-set-based-operations-instead-of-row-by-row-processing-when-possible)
  - [SQL-PERF-004 - Avoid long-running transactions that hold locks unnecessarily.](#sql-perf-004---avoid-long-running-transactions-that-hold-locks-unnecessarily)
  - [SQL-PERF-005 - Monitor key performance metrics such as buffer cache hit ratio and wait stats.](#sql-perf-005---monitor-key-performance-metrics-such-as-buffer-cache-hit-ratio-and-wait-stats)
  - [SQL-PERF-006 - Utilise partitioning or sharding when data volume exceeds single-instance limits.](#sql-perf-006---utilise-partitioning-or-sharding-when-data-volume-exceeds-single-instance-limits)
- [`languages/sql/sql-security.yml`](#languagessqlsql-securityyml)
  - [SQL-SEC-001 - Apply `../../common/compliance.yml` controls before database-specific security measures.](#sql-sec-001---apply-commoncomplianceyml-controls-before-database-specific-security-measures)
  - [SQL-SEC-002 - Use parametrised queries or stored procedures to avoid injection attacks.](#sql-sec-002---use-parametrised-queries-or-stored-procedures-to-avoid-injection-attacks)
  - [SQL-SEC-003 - Restrict database permissions to the minimum required roles.](#sql-sec-003---restrict-database-permissions-to-the-minimum-required-roles)
  - [SQL-SEC-004 - Encrypt sensitive data at rest using database-native features.](#sql-sec-004---encrypt-sensitive-data-at-rest-using-database-native-features)
  - [SQL-SEC-005 - Sanitise dynamic SQL fragments and whitelist allowable identifiers.](#sql-sec-005---sanitise-dynamic-sql-fragments-and-whitelist-allowable-identifiers)
  - [SQL-SEC-006 - Audit access to high-risk tables and views.](#sql-sec-006---audit-access-to-high-risk-tables-and-views)
- [`languages/sql/sql-style.yml`](#languagessqlsql-styleyml)
  - [SQL-STYLE-001 - Apply `../../common/base.yml` guidance before database-specific style conventions.](#sql-style-001---apply-commonbaseyml-guidance-before-database-specific-style-conventions)
  - [SQL-STYLE-002 - Use uppercase for SQL keywords and snake_case for identifiers unless standards differ.](#sql-style-002---use-uppercase-for-sql-keywords-and-snake_case-for-identifiers-unless-standards-differ)
  - [SQL-STYLE-003 - Format queries with aligned SELECT columns and clauses for readability.](#sql-style-003---format-queries-with-aligned-select-columns-and-clauses-for-readability)
  - [SQL-STYLE-004 - Alias tables meaningfully when joins are present.](#sql-style-004---alias-tables-meaningfully-when-joins-are-present)
  - [SQL-STYLE-005 - Avoid SELECT * in production code; list required columns.](#sql-style-005---avoid-select--in-production-code-list-required-columns)
  - [SQL-STYLE-006 - Document complex queries with inline comments summarising intent.](#sql-style-006---document-complex-queries-with-inline-comments-summarising-intent)
- [`languages/sql/sql-testing.yml`](#languagessqlsql-testingyml)
  - [SQL-TEST-001 - Follow `../../common/tdd_bdd.yml` before applying database-specific testing practices.](#sql-test-001---follow-commontdd_bddyml-before-applying-database-specific-testing-practices)
  - [SQL-TEST-002 - Write automated database tests to validate schema changes and stored procedures.](#sql-test-002---write-automated-database-tests-to-validate-schema-changes-and-stored-procedures)
  - [SQL-TEST-003 - Use transactional tests that roll back changes after assertions.](#sql-test-003---use-transactional-tests-that-roll-back-changes-after-assertions)
  - [SQL-TEST-004 - Populate test data sets deterministically to ensure repeatability.](#sql-test-004---populate-test-data-sets-deterministically-to-ensure-repeatability)
  - [SQL-TEST-005 - Verify execution plans for critical queries in performance-sensitive paths.](#sql-test-005---verify-execution-plans-for-critical-queries-in-performance-sensitive-paths)
  - [SQL-TEST-006 - Automate migration validation in CI before deploying to shared environments.](#sql-test-006---automate-migration-validation-in-ci-before-deploying-to-shared-environments)

### `languages/sql/sql-architecture.yml`
Goal: Govern relational design choices so schemas stay maintainable and audit-friendly.

#### SQL-ARCH-001 - Apply `../../common/architecture.yml` principles before database-specific architecture decisions.
- **Intent:** Shared principles ensure database design aligns with system-wide constraints.
- **Example:** Confirm domain boundaries from the common policy before modelling a new schema.
[Back to top](#top)

#### SQL-ARCH-002 - Normalise schemas to the agreed level unless denormalisation is justified.
- **Intent:** Normalisation prevents redundant data and update anomalies.
- **Example:** Split customer addresses into reference tables unless reporting proves denormalisation is needed.
[Back to top](#top)

#### SQL-ARCH-003 - Design primary keys as stable, unique identifiers and avoid natural keys when volatile.
- **Intent:** Stable keys simplify migrations and integrations.
- **Example:** Use surrogate keys like `CustomerId` instead of relying on email addresses.
[Back to top](#top)

#### SQL-ARCH-004 - Version database schema changes using migrations tracked in source control.
- **Intent:** Versioned migrations provide traceability and rollback options.
- **Example:** Store Liquibase or Flyway scripts alongside application code for review.
[Back to top](#top)

#### SQL-ARCH-005 - Define indexes based on workload analysis and maintain them proactively.
- **Intent:** Targeted indexes balance performance with storage and maintenance cost.
- **Example:** Add covering indexes after analysing slow query logs instead of indexing every column.
[Back to top](#top)

#### SQL-ARCH-006 - Separate OLTP and analytics workloads to prevent resource contention.
- **Intent:** Dedicated workloads prevent reporting queries from starving transactional traffic.
- **Example:** Replicate data to a reporting warehouse rather than running heavy aggregations on the production OLTP server.
[Back to top](#top)

### `languages/sql/sql-performance.yml`
Goal: Keep databases fast through measurement, set-based design, and disciplined resource usage.

#### SQL-PERF-001 - Apply `../../common/performance.yml` budgets and practices before database-specific optimisations.
- **Intent:** Shared metrics align database tuning with overall performance goals.
- **Example:** Validate end-to-end latency targets before redesigning indexes.
[Back to top](#top)

#### SQL-PERF-002 - Review query execution plans for regressions before release.
- **Intent:** Plan analysis catches regressions early.
- **Example:** Compare execution plans between releases to detect missing indexes.
[Back to top](#top)

#### SQL-PERF-003 - Use set-based operations instead of row-by-row processing when possible.
- **Intent:** Set-based queries leverage the engine’s optimiser for efficiency.
- **Example:** Replace cursor loops with a single `UPDATE ... WHERE` statement.
[Back to top](#top)

#### SQL-PERF-004 - Avoid long-running transactions that hold locks unnecessarily.
- **Intent:** Short transactions reduce contention and deadlocks.
- **Example:** Commit batches of work frequently instead of wrapping an entire ETL job in one transaction.
[Back to top](#top)

#### SQL-PERF-005 - Monitor key performance metrics such as buffer cache hit ratio and wait stats.
- **Intent:** Observability guides targeted improvements.
- **Example:** Use DMV queries or monitoring tools to watch wait stats before tuning indexes.
[Back to top](#top)

#### SQL-PERF-006 - Utilise partitioning or sharding when data volume exceeds single-instance limits.
- **Intent:** Horizontal scaling maintains performance for very large datasets.
- **Example:** Partition a time-series table by month to keep maintenance windows manageable.
[Back to top](#top)

### `languages/sql/sql-security.yml`
Goal: Protect data stores by enforcing least privilege, safe query construction, and auditable controls.

#### SQL-SEC-001 - Apply `../../common/compliance.yml` controls before database-specific security measures.
- **Intent:** Compliance dictates baseline protections before engine-specific tactics.
- **Example:** Confirm encryption requirements in the common policy before configuring TDE.
[Back to top](#top)

#### SQL-SEC-002 - Use parametrised queries or stored procedures to avoid injection attacks.
- **Intent:** Bound parameters neutralise malicious input.
- **Example:** Call stored procedures with parameters instead of concatenating SQL strings.
[Back to top](#top)

#### SQL-SEC-003 - Restrict database permissions to the minimum required roles.
- **Intent:** Least privilege limits blast radius for compromised accounts.
- **Example:** Grant applications execute rights on stored procedures rather than dbo-level access.
[Back to top](#top)

#### SQL-SEC-004 - Encrypt sensitive data at rest using database-native features.
- **Intent:** Native encryption simplifies compliance and key rotation.
- **Example:** Enable Transparent Data Encryption with managed keys for regulated tables.
[Back to top](#top)

#### SQL-SEC-005 - Sanitise dynamic SQL fragments and whitelist allowable identifiers.
- **Intent:** Sanitisation prevents attackers from injecting table or column names.
- **Example:** Validate user-supplied column lists against a whitelist before constructing dynamic SQL.
[Back to top](#top)

#### SQL-SEC-006 - Audit access to high-risk tables and views.
- **Intent:** Audit trails support incident response and compliance evidence.
- **Example:** Enable native audit logging for tables containing personal data and review reports regularly.
[Back to top](#top)

### `languages/sql/sql-style.yml`
Goal: Standardise SQL readability so queries remain easy to review and maintain.

#### SQL-STYLE-001 - Apply `../../common/base.yml` guidance before database-specific style conventions.
- **Intent:** Base style alignment comes first across the repository.
- **Example:** Confirm base expectations on naming and comments before referencing SQL-specific rules.
[Back to top](#top)

#### SQL-STYLE-002 - Use uppercase for SQL keywords and snake_case for identifiers unless standards differ.
- **Intent:** Consistent casing improves scanning and review.
- **Example:** Write `SELECT order_id FROM sales_order` instead of mixed-case keywords.
[Back to top](#top)

#### SQL-STYLE-003 - Format queries with aligned SELECT columns and clauses for readability.
- **Intent:** Aligned formatting clarifies intent and simplifies diff reviews.
- **Example:** Place each selected column on its own line under the `SELECT` keyword.
[Back to top](#top)

#### SQL-STYLE-004 - Alias tables meaningfully when joins are present.
- **Intent:** Descriptive aliases signal relationships without cryptic abbreviations.
- **Example:** Alias `orders` as `o` and `order_items` as `oi` instead of single letters.
[Back to top](#top)

#### SQL-STYLE-005 - Avoid SELECT * in production code; list required columns.
- **Intent:** Explicit columns prevent accidental data exposure and reduce IO.
- **Example:** Enumerate only needed fields in reports rather than using `SELECT *`.
[Back to top](#top)

#### SQL-STYLE-006 - Document complex queries with inline comments summarising intent.
- **Intent:** Comments give reviewers context without reverse-engineering logic.
- **Example:** Add `-- Join to ensure only active subscriptions` before non-obvious joins.
[Back to top](#top)

### `languages/sql/sql-testing.yml`
Goal: Validate database changes safely with automated, repeatable checks.

#### SQL-TEST-001 - Follow `../../common/tdd_bdd.yml` before applying database-specific testing practices.
- **Intent:** Shared testing discipline structures specs and conversations.
- **Example:** Draft a failing behaviour scenario for a stored procedure before changing it.
[Back to top](#top)

#### SQL-TEST-002 - Write automated database tests to validate schema changes and stored procedures.
- **Intent:** Automated coverage catches regressions beyond manual scripts.
- **Example:** Execute migration verification scripts in CI after each PR.
[Back to top](#top)

#### SQL-TEST-003 - Use transactional tests that roll back changes after assertions.
- **Intent:** Rollbacks keep test runs isolated and repeatable.
- **Example:** Wrap integration tests in transactions that `ROLLBACK` at the end of each case.
[Back to top](#top)

#### SQL-TEST-004 - Populate test data sets deterministically to ensure repeatability.
- **Intent:** Stable fixtures avoid flakiness due to random data.
- **Example:** Seed key tables with known identifiers before executing verification queries.
[Back to top](#top)

#### SQL-TEST-005 - Verify execution plans for critical queries in performance-sensitive paths.
- **Intent:** Ensuring plan quality prevents hidden regressions from creeping in.
- **Example:** Capture `EXPLAIN` output for analytics queries as part of regression tests.
[Back to top](#top)

#### SQL-TEST-006 - Automate migration validation in CI before deploying to shared environments.
- **Intent:** Automated validation prevents failing migrations from reaching integration or production stages.
- **Example:** Run migration apply/rollback scripts inside CI containers before tagging a release.
[Back to top](#top)

## 3. Applying These Guidelines
Combine the SQL YAML policies as part of your prompt bundle, and draw from these rationales during reviews, design sessions, or audits to justify database implementation decisions.
[Back to top](#top)
