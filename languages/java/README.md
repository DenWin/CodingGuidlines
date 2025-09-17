<a id="top"></a>
# Java Policy Rationale

## 1. About This README
This guide expands on the Java-specific YAML rules covering architecture, performance, security, style, and testing. Reference it when you need to understand the motivation behind each rule, see concrete examples, or brief human collaborators without inflating prompts.
Treat the YAML files as the machine-readable source of truth. Use this README to educate humans, onboard new collaborators, or justify decisions during reviews. When adjusting rules, update both the YAML file and the relevant rationale entry to keep them aligned.

## 2. File-by-File Guidance
- [`languages/java/java-architecture.yml`](#languagesjavajava-architectureyml)
  - [JAVA-ARCH-001 - Apply `../../common/architecture.yml` principles before adding Java-specific architecture choices.](#java-arch-001---apply-commonarchitectureyml-principles-before-adding-java-specific-architecture-choices)
  - [JAVA-ARCH-002 - Separate API, service, and persistence layers with explicit interfaces.](#java-arch-002---separate-api-service-and-persistence-layers-with-explicit-interfaces)
  - [JAVA-ARCH-003 - Use Spring Boot auto-configuration responsibly by overriding defaults only when necessary.](#java-arch-003---use-spring-boot-auto-configuration-responsibly-by-overriding-defaults-only-when-necessary)
  - [JAVA-ARCH-004 - Keep domain logic free of framework annotations where feasible.](#java-arch-004---keep-domain-logic-free-of-framework-annotations-where-feasible)
  - [JAVA-ARCH-005 - Expose asynchronous workloads through managed executors or messaging platforms.](#java-arch-005---expose-asynchronous-workloads-through-managed-executors-or-messaging-platforms)
  - [JAVA-ARCH-006 - Version REST APIs and maintain backward compatibility for active consumers.](#java-arch-006---version-rest-apis-and-maintain-backward-compatibility-for-active-consumers)
- [`languages/java/java-performance.yml`](#languagesjavajava-performanceyml)
  - [JAVA-PERF-001 - Apply `../../common/performance.yml` budgets and practices before Java-specific tuning.](#java-perf-001---apply-commonperformanceyml-budgets-and-practices-before-java-specific-tuning)
  - [JAVA-PERF-002 - Prefer streams for bulk operations only when they do not obscure complexity.](#java-perf-002---prefer-streams-for-bulk-operations-only-when-they-do-not-obscure-complexity)
  - [JAVA-PERF-003 - Reuse thread pools provided by the runtime instead of creating unmanaged threads.](#java-perf-003---reuse-thread-pools-provided-by-the-runtime-instead-of-creating-unmanaged-threads)
  - [JAVA-PERF-004 - Profile JVM memory usage before tuning garbage collection settings.](#java-perf-004---profile-jvm-memory-usage-before-tuning-garbage-collection-settings)
  - [JAVA-PERF-005 - Close IO resources deterministically using try-with-resources.](#java-perf-005---close-io-resources-deterministically-using-try-with-resources)
  - [JAVA-PERF-006 - Prefer immutable data structures for shared-read workloads to reduce locking.](#java-perf-006---prefer-immutable-data-structures-for-shared-read-workloads-to-reduce-locking)
- [`languages/java/java-security.yml`](#languagesjavajava-securityyml)
  - [JAVA-SEC-001 - Apply `../../common/compliance.yml` controls before Java-specific security measures.](#java-sec-001---apply-commoncomplianceyml-controls-before-java-specific-security-measures)
  - [JAVA-SEC-002 - Use parameterised queries or ORM features to prevent SQL injection.](#java-sec-002---use-parameterised-queries-or-orm-features-to-prevent-sql-injection)
  - [JAVA-SEC-003 - Validate and sanitise all external inputs before processing.](#java-sec-003---validate-and-sanitise-all-external-inputs-before-processing)
  - [JAVA-SEC-004 - Prefer the Java Cryptography Architecture over custom crypto implementations.](#java-sec-004---prefer-the-java-cryptography-architecture-over-custom-crypto-implementations)
  - [JAVA-SEC-005 - Enable secure defaults such as HTTP security headers and TLS enforcement.](#java-sec-005---enable-secure-defaults-such-as-http-security-headers-and-tls-enforcement)
- [`languages/java/java-style.yml`](#languagesjavajava-styleyml)
  - [JAVA-STYLE-001 - Apply `../../common/base.yml` guidance before enforcing Java-specific style rules.](#java-style-001---apply-commonbaseyml-guidance-before-enforcing-java-specific-style-rules)
  - [JAVA-STYLE-002 - Follow standard Java naming conventions for classes, methods, and constants.](#java-style-002---follow-standard-java-naming-conventions-for-classes-methods-and-constants)
  - [JAVA-STYLE-003 - Use builder patterns or constructors for object creation instead of telescoping setters.](#java-style-003---use-builder-patterns-or-constructors-for-object-creation-instead-of-telescoping-setters)
  - [JAVA-STYLE-004 - Annotate overrides with `@Override` for clarity and compile-time checks.](#java-style-004---annotate-overrides-with-override-for-clarity-and-compile-time-checks)
  - [JAVA-STYLE-005 - Prefer `var` only in local scopes where the inferred type is obvious.](#java-style-005---prefer-var-only-in-local-scopes-where-the-inferred-type-is-obvious)
- [`languages/java/java-testing.yml`](#languagesjavajava-testingyml)
  - [JAVA-TEST-001 - Follow `../../common/tdd_bdd.yml` before applying Java-specific testing practices.](#java-test-001---follow-commontdd_bddyml-before-applying-java-specific-testing-practices)
  - [JAVA-TEST-002 - Use JUnit 5 for new unit and integration tests unless otherwise specified.](#java-test-002---use-junit-5-for-new-unit-and-integration-tests-unless-otherwise-specified)
  - [JAVA-TEST-003 - Mock external systems using dedicated test doubles instead of shared fixtures.](#java-test-003---mock-external-systems-using-dedicated-test-doubles-instead-of-shared-fixtures)
  - [JAVA-TEST-004 - Name tests to describe behaviour under test and expected outcome.](#java-test-004---name-tests-to-describe-behaviour-under-test-and-expected-outcome)
  - [JAVA-TEST-005 - Fail tests on unchecked exceptions rather than catching them silently.](#java-test-005---fail-tests-on-unchecked-exceptions-rather-than-catching-them-silently)
  - [JAVA-TEST-006 - Tag slow tests so pipelines can isolate or parallelise them.](#java-test-006---tag-slow-tests-so-pipelines-can-isolate-or-parallelise-them)

### `languages/java/java-architecture.yml`
Goal: Keep JVM services modular while balancing Spring convenience with explicit domain boundaries.

#### JAVA-ARCH-001 - Apply `../../common/architecture.yml` principles before adding Java-specific architecture choices.
- **Intent:** Shared rules anchor platform design so JVM guidance builds on a stable base.
- **Example:** Confirm layering expectations from the common policy before sketching a Spring Boot bounded context.
[Back to top](#top)

#### JAVA-ARCH-002 - Separate API, service, and persistence layers with explicit interfaces.
- **Intent:** Boundary contracts simplify testing and swapping infrastructure.
- **Example:** Expose repository interfaces in the service layer instead of injecting `EntityManager` into controllers.
[Back to top](#top)

#### JAVA-ARCH-003 - Use Spring Boot auto-configuration responsibly by overriding defaults only when necessary.
- **Intent:** Minimal overrides keep upgrades predictable and avoid hidden coupling.
- **Example:** Accept Spring’s default JSON converter unless audit requirements demand a custom serializer.
[Back to top](#top)

#### JAVA-ARCH-004 - Keep domain logic free of framework annotations where feasible.
- **Intent:** Plain domain objects remain portable and easier to test.
- **Example:** Use value objects without `@Component` annotations and inject them through services instead.
[Back to top](#top)

#### JAVA-ARCH-005 - Expose asynchronous workloads through managed executors or messaging platforms.
- **Intent:** Managed infrastructure provides back-pressure and observability.
- **Example:** Dispatch long-running exports via `@Async` executors rather than spinning raw threads.
[Back to top](#top)

#### JAVA-ARCH-006 - Version REST APIs and maintain backward compatibility for active consumers.
- **Intent:** Versioning shields clients from breaking changes during rollout.
- **Example:** Keep `/api/v1/orders` live until all consumers migrate to `/api/v2/orders` and document the timeline.
[Back to top](#top)

### `languages/java/java-performance.yml`
Goal: Optimise JVM applications with measured, maintainable techniques rather than speculative tuning.

#### JAVA-PERF-001 - Apply `../../common/performance.yml` budgets and practices before Java-specific tuning.
- **Intent:** Shared budgets prevent micro-optimisations from eclipsing cross-team expectations.
- **Example:** Check the common latency target before reworking caches for a feature.
[Back to top](#top)

#### JAVA-PERF-002 - Prefer streams for bulk operations only when they do not obscure complexity.
- **Intent:** Streams should clarify intent, not hide expensive operations.
- **Example:** Replace a nested stream chain with a simple loop when it requires multiple terminal operations.
[Back to top](#top)

#### JAVA-PERF-003 - Reuse thread pools provided by the runtime instead of creating unmanaged threads.
- **Intent:** Shared pools avoid resource exhaustion and simplify monitoring.
- **Example:** Submit tasks to `TaskExecutor` beans rather than constructing new `Thread` objects.
[Back to top](#top)

#### JAVA-PERF-004 - Profile JVM memory usage before tuning garbage collection settings.
- **Intent:** Measurements inform whether GC tweaks will help or harm.
- **Example:** Capture heap dumps with `jcmd` prior to adjusting G1 region sizes.
[Back to top](#top)

#### JAVA-PERF-005 - Close IO resources deterministically using try-with-resources.
- **Intent:** Automatic closure prevents descriptor leaks and cascading timeouts.
- **Example:** Wrap `InputStream` handling in `try (var in = ...)` blocks so it closes on exception.
[Back to top](#top)

#### JAVA-PERF-006 - Prefer immutable data structures for shared-read workloads to reduce locking.
- **Intent:** Immutable objects eliminate contention across threads.
- **Example:** Publish configuration snapshots as `List.copyOf(...)` rather than using synchronised lists.
[Back to top](#top)

### `languages/java/java-security.yml`
Goal: Extend compliance requirements with JVM-friendly defences for data access and transport security.

#### JAVA-SEC-001 - Apply `../../common/compliance.yml` controls before Java-specific security measures.
- **Intent:** Compliance dictates baseline controls independent of language.
- **Example:** Honour data localisation mandates before provisioning Spring profiles for each region.
[Back to top](#top)

#### JAVA-SEC-002 - Use parameterised queries or ORM features to prevent SQL injection.
- **Intent:** Bound parameters block hostile input from altering queries.
- **Example:** Replace string concatenated HQL with named parameters or JPA criteria builders.
[Back to top](#top)

#### JAVA-SEC-003 - Validate and sanitise all external inputs before processing.
- **Intent:** Input checks prevent malicious payloads from reaching core logic.
- **Example:** Apply Bean Validation annotations and reject payloads that fail constraint checks.
[Back to top](#top)

#### JAVA-SEC-004 - Prefer the Java Cryptography Architecture over custom crypto implementations.
- **Intent:** JCA offers vetted algorithms and managed key handling.
- **Example:** Use `Cipher.getInstance("AES/GCM/NoPadding")` instead of bundling ad-hoc encryption utilities.
[Back to top](#top)

#### JAVA-SEC-005 - Enable secure defaults such as HTTP security headers and TLS enforcement.
- **Intent:** Hardened defaults reduce the chance of misconfiguration leaks.
- **Example:** Configure Spring Security to enforce HTTPS and add HSTS headers on all responses.
[Back to top](#top)

### `languages/java/java-style.yml`
Goal: Maintain idiomatic Java code so teams can rely on familiar conventions and automated tooling.

#### JAVA-STYLE-001 - Apply `../../common/base.yml` guidance before enforcing Java-specific style rules.
- **Intent:** Base styling ensures cross-language cohesion.
- **Example:** Confirm comment and readability expectations from the base policy before citing Java style.
[Back to top](#top)

#### JAVA-STYLE-002 - Follow standard Java naming conventions for classes, methods, and constants.
- **Intent:** Conventional names improve comprehension and IDE navigation.
- **Example:** Name constants `MAX_ITEMS` and classes `OrderService` rather than ad-hoc casing.
[Back to top](#top)

#### JAVA-STYLE-003 - Use builder patterns or constructors for object creation instead of telescoping setters.
- **Intent:** Builders maintain immutability and reduce invalid intermediate states.
- **Example:** Replace chains of `setX` calls with a Lombok builder when constructing DTOs.
[Back to top](#top)

#### JAVA-STYLE-004 - Annotate overrides with `@Override` for clarity and compile-time checks.
- **Intent:** The annotation prevents signature drift and signals intent.
- **Example:** Mark service overrides of interface methods with `@Override` to catch method renames.
[Back to top](#top)

#### JAVA-STYLE-005 - Prefer `var` only in local scopes where the inferred type is obvious.
- **Intent:** Controlled use of `var` keeps code concise without losing meaning.
- **Example:** Use `var count = orders.size();` but spell out `Map<String, List<Order>>` for nested generics.
[Back to top](#top)

### `languages/java/java-testing.yml`
Goal: Keep JVM test suites fast, expressive, and deterministic across environments.

#### JAVA-TEST-001 - Follow `../../common/tdd_bdd.yml` before applying Java-specific testing practices.
- **Intent:** Shared testing discipline shapes scenario design before tooling choices.
- **Example:** Write a failing behaviour-focused JUnit test before implementing the service method.
[Back to top](#top)

#### JAVA-TEST-002 - Use JUnit 5 for new unit and integration tests unless otherwise specified.
- **Intent:** A single framework streamlines shared utilities and IDE support.
- **Example:** Create new test classes with the JUnit 5 Jupiter API instead of mixing in legacy JUnit 4.
[Back to top](#top)

#### JAVA-TEST-003 - Mock external systems using dedicated test doubles instead of shared fixtures.
- **Intent:** Targeted doubles isolate behaviour and avoid cross-test coupling.
- **Example:** Replace a shared database fixture with a `@MockBean` repository in Spring tests.
[Back to top](#top)

#### JAVA-TEST-004 - Name tests to describe behaviour under test and expected outcome.
- **Intent:** Descriptive names turn test output into actionable documentation.
- **Example:** Use `shouldRejectOrderWhenInventoryMissing()` instead of `testOrder()`.
[Back to top](#top)

#### JAVA-TEST-005 - Fail tests on unchecked exceptions rather than catching them silently.
- **Intent:** Surfacing exceptions guards against hidden regressions.
- **Example:** Let a `RuntimeException` bubble out of a test rather than wrapping it in `try/catch` with no assertion.
[Back to top](#top)

#### JAVA-TEST-006 - Tag slow tests so pipelines can isolate or parallelise them.
- **Intent:** Tags enable customised CI stages and faster feedback.
- **Example:** Annotate long-running integration tests with `@Tag("slow")` and filter them into a nightly stage.
[Back to top](#top)

## 3. Applying These Guidelines
Compose the relevant YAML files into your agent prompts, and lean on these rationales during reviews or design sessions when Java-specific trade-offs need explanation.
[Back to top](#top)
