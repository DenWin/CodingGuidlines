<a id="top"></a>
# C# Policy Rationale

## 1. About This README
This document elaborates on the C# policies spanning architecture, performance, security, style, and testing. Use it for onboarding, reviews, or anytime the terse YAML rules require background detail plus concrete examples.
Treat the YAML files as the machine-readable source of truth. Use this README to educate humans, onboard new collaborators, or justify decisions during reviews. When adjusting rules, update both the YAML file and the relevant rationale entry to keep them aligned.

## 2. File-by-File Guidance
- [`languages/csharp/csharp-architecture.yml`](#languagescsharpcsharp-architectureyml)
  - [CSHARP-ARCH-001 - Apply `../../common/architecture.yml` principles before adding C#-specific architecture guidance.](#csharp-arch-001---apply-commonarchitectureyml-principles-before-adding-c-specific-architecture-guidance)
  - [CSHARP-ARCH-002 - Separate application, domain, and infrastructure layers with clear interfaces.](#csharp-arch-002---separate-application-domain-and-infrastructure-layers-with-clear-interfaces)
  - [CSHARP-ARCH-003 - Use dependency injection containers for service wiring instead of manual factories.](#csharp-arch-003---use-dependency-injection-containers-for-service-wiring-instead-of-manual-factories)
  - [CSHARP-ARCH-004 - Keep Entity Framework entities decoupled from API contracts via DTOs.](#csharp-arch-004---keep-entity-framework-entities-decoupled-from-api-contracts-via-dtos)
  - [CSHARP-ARCH-005 - Prefer asynchronous controllers and handlers for IO-bound operations.](#csharp-arch-005---prefer-asynchronous-controllers-and-handlers-for-io-bound-operations)
  - [CSHARP-ARCH-006 - Version public APIs and maintain compatibility for active integrations.](#csharp-arch-006---version-public-apis-and-maintain-compatibility-for-active-integrations)
- [`languages/csharp/csharp-performance.yml`](#languagescsharpcsharp-performanceyml)
  - [CSHARP-PERF-001 - Apply `../../common/performance.yml` budgets and practices before C#-specific optimisations.](#csharp-perf-001---apply-commonperformanceyml-budgets-and-practices-before-c-specific-optimisations)
  - [CSHARP-PERF-002 - Use Span and Memory constructs to reduce allocations in hot paths.](#csharp-perf-002---use-span-and-memory-constructs-to-reduce-allocations-in-hot-paths)
  - [CSHARP-PERF-003 - Dispose `IDisposable` implementations deterministically via `using` scopes.](#csharp-perf-003---dispose-idisposable-implementations-deterministically-via-using-scopes)
  - [CSHARP-PERF-004 - Profile async call chains to avoid oversubscription of the thread pool.](#csharp-perf-004---profile-async-call-chains-to-avoid-oversubscription-of-the-thread-pool)
  - [CSHARP-PERF-005 - Prefer `ValueTask` for frequently awaited low-cost operations.](#csharp-perf-005---prefer-valuetask-for-frequently-awaited-low-cost-operations)
  - [CSHARP-PERF-006 - Avoid synchronous over async bridging on the hot path to prevent deadlocks.](#csharp-perf-006---avoid-synchronous-over-async-bridging-on-the-hot-path-to-prevent-deadlocks)
- [`languages/csharp/csharp-security.yml`](#languagescsharpcsharp-securityyml)
  - [CSHARP-SEC-001 - Apply `../../common/compliance.yml` controls before C#-specific security measures.](#csharp-sec-001---apply-commoncomplianceyml-controls-before-c-specific-security-measures)
  - [CSHARP-SEC-002 - Use parameterised queries or ORM safeguards to prevent injection.](#csharp-sec-002---use-parameterised-queries-or-orm-safeguards-to-prevent-injection)
  - [CSHARP-SEC-003 - Validate model binding inputs and enforce data annotations server-side.](#csharp-sec-003---validate-model-binding-inputs-and-enforce-data-annotations-server-side)
  - [CSHARP-SEC-004 - Use ASP.NET Core data protection APIs for encryption and key management.](#csharp-sec-004---use-aspnet-core-data-protection-apis-for-encryption-and-key-management)
  - [CSHARP-SEC-005 - Enable antiforgery tokens for state-changing HTTP endpoints.](#csharp-sec-005---enable-antiforgery-tokens-for-state-changing-http-endpoints)
- [`languages/csharp/csharp-style.yml`](#languagescsharpcsharp-styleyml)
  - [CSHARP-STYLE-001 - Apply `../../common/base.yml` guidance before enforcing C#-specific style rules.](#csharp-style-001---apply-commonbaseyml-guidance-before-enforcing-c-specific-style-rules)
  - [CSHARP-STYLE-002 - Follow .NET naming conventions for namespaces, types, and members.](#csharp-style-002---follow-net-naming-conventions-for-namespaces-types-and-members)
  - [CSHARP-STYLE-003 - Use expression-bodied members only when the body remains trivial.](#csharp-style-003---use-expression-bodied-members-only-when-the-body-remains-trivial)
  - [CSHARP-STYLE-004 - Place nullable reference annotations consistently across public APIs.](#csharp-style-004---place-nullable-reference-annotations-consistently-across-public-apis)
  - [CSHARP-STYLE-005 - Prefer `var` when the inferred type is obvious and aids readability.](#csharp-style-005---prefer-var-when-the-inferred-type-is-obvious-and-aids-readability)
  - [CSHARP-STYLE-006 - Organise usings with system directives first and remove unused imports.](#csharp-style-006---organise-usings-with-system-directives-first-and-remove-unused-imports)
- [`languages/csharp/csharp-testing.yml`](#languagescsharpcsharp-testingyml)
  - [CSHARP-TEST-001 - Follow `../../common/tdd_bdd.yml` before applying C#-specific testing practices.](#csharp-test-001---follow-commontdd_bddyml-before-applying-c-specific-testing-practices)
  - [CSHARP-TEST-002 - Use xUnit or the team-standard framework for new tests.](#csharp-test-002---use-xunit-or-the-team-standard-framework-for-new-tests)
  - [CSHARP-TEST-003 - Isolate external dependencies with test doubles or in-memory providers.](#csharp-test-003---isolate-external-dependencies-with-test-doubles-or-in-memory-providers)
  - [CSHARP-TEST-004 - Assert on behavioural outcomes rather than implementation details.](#csharp-test-004---assert-on-behavioural-outcomes-rather-than-implementation-details)
  - [CSHARP-TEST-005 - Apply data-driven tests for permutations instead of duplicating logic.](#csharp-test-005---apply-data-driven-tests-for-permutations-instead-of-duplicating-logic)
  - [CSHARP-TEST-006 - Keep unit tests deterministic and independent of system time or locale.](#csharp-test-006---keep-unit-tests-deterministic-and-independent-of-system-time-or-locale)

### `languages/csharp/csharp-architecture.yml`
Goal: Keep .NET solutions layered cleanly so web, domain, and infrastructure code remain independently testable.

#### CSHARP-ARCH-001 - Apply `../../common/architecture.yml` principles before adding C#-specific architecture guidance.
- **Intent:** Common architectural rules prevent divergence before platform nuances are considered.
- **Example:** Review domain boundary guidance from the shared policy before sketching a new ASP.NET Core module.
[Back to top](#top)

#### CSHARP-ARCH-002 - Separate application, domain, and infrastructure layers with clear interfaces.
- **Intent:** Layered seams allow swapping persistence or transports without rewriting domain logic.
- **Example:** Expose repositories via interfaces consumed by services rather than referencing `DbContext` from controllers.
[Back to top](#top)

#### CSHARP-ARCH-003 - Use dependency injection containers for service wiring instead of manual factories.
- **Intent:** The DI container enforces composition roots and simplifies testing with mocks.
- **Example:** Register command handlers in `Program.cs` rather than new-ing them inside MVC controllers.
[Back to top](#top)

#### CSHARP-ARCH-004 - Keep Entity Framework entities decoupled from API contracts via DTOs.
- **Intent:** DTOs prevent leaking persistence concerns and enable versioned responses.
- **Example:** Map `OrderEntity` to `OrderResponse` using AutoMapper before returning data from an API.
[Back to top](#top)

#### CSHARP-ARCH-005 - Prefer asynchronous controllers and handlers for IO-bound operations.
- **Intent:** Async endpoints free the thread pool and scale under bursty load.
- **Example:** Make repository calls with `await` and return `Task<IActionResult>` for database-backed endpoints.
[Back to top](#top)

#### CSHARP-ARCH-006 - Version public APIs and maintain compatibility for active integrations.
- **Intent:** Versioning protects consumers while services evolve.
- **Example:** Keep v1 controllers online until all partners migrate to v2 and document deprecation timelines.
[Back to top](#top)

### `languages/csharp/csharp-performance.yml`
Goal: Optimise managed code pragmatically so CLR services stay responsive without premature micro-tuning.

#### CSHARP-PERF-001 - Apply `../../common/performance.yml` budgets and practices before C#-specific optimisations.
- **Intent:** Shared performance expectations frame language-specific trade-offs.
- **Example:** Confirm the feature’s latency budget from the common policy before chasing allocations.
[Back to top](#top)

#### CSHARP-PERF-002 - Use Span and Memory constructs to reduce allocations in hot paths.
- **Intent:** Stack-friendly buffers lessen GC pressure inside tight loops.
- **Example:** Replace substring slicing with `ReadOnlySpan<char>` when parsing large payloads.
[Back to top](#top)

#### CSHARP-PERF-003 - Dispose `IDisposable` implementations deterministically via `using` scopes.
- **Intent:** Predictable disposal releases scarce handles quickly.
- **Example:** Wrap `SqlConnection` usage in `await using` so it closes even when exceptions occur.
[Back to top](#top)

#### CSHARP-PERF-004 - Profile async call chains to avoid oversubscription of the thread pool.
- **Intent:** Async flood can starve worker threads if scheduling is opaque.
- **Example:** Use `dotnet-trace` to inspect queue lengths before launching additional background tasks.
[Back to top](#top)

#### CSHARP-PERF-005 - Prefer `ValueTask` for frequently awaited low-cost operations.
- **Intent:** `ValueTask` avoids heap allocations in hot, synchronous paths.
- **Example:** Return `ValueTask` from cache lookups that usually complete synchronously.
[Back to top](#top)

#### CSHARP-PERF-006 - Avoid synchronous over async bridging on the hot path to prevent deadlocks.
- **Intent:** Blocking on async results risks deadlocks and throttles throughput.
- **Example:** Remove `.Result` on HTTP calls inside ASP.NET controllers and await the task instead.
[Back to top](#top)

### `languages/csharp/csharp-security.yml`
Goal: Extend compliance foundations with platform-aware safeguards for ASP.NET Core services and libraries.

#### CSHARP-SEC-001 - Apply `../../common/compliance.yml` controls before C#-specific security measures.
- **Intent:** Compliance guardrails dictate mandatory controls before implementation details.
- **Example:** Confirm data residency requirements from the common policy before selecting KMS regions.
[Back to top](#top)

#### CSHARP-SEC-002 - Use parameterised queries or ORM safeguards to prevent injection.
- **Intent:** Parameters neutralise hostile input reaching SQL engines.
- **Example:** Replace string concatenated SQL with EF Core LINQ or `SqlParameter` bindings.
[Back to top](#top)

#### CSHARP-SEC-003 - Validate model binding inputs and enforce data annotations server-side.
- **Intent:** Server validation blocks tampered requests regardless of front-end state.
- **Example:** Apply `[Required]` and `[Range]` attributes then check `ModelState.IsValid` before executing logic.
[Back to top](#top)

#### CSHARP-SEC-004 - Use ASP.NET Core data protection APIs for encryption and key management.
- **Intent:** Built-in providers rotate keys safely and integrate with the hosting environment.
- **Example:** Protect cookies with `IDataProtector` rather than hand-rolled AES helpers.
[Back to top](#top)

#### CSHARP-SEC-005 - Enable antiforgery tokens for state-changing HTTP endpoints.
- **Intent:** CSRF protection protects authenticated workflows from forged requests.
- **Example:** Decorate POST actions with `[ValidateAntiForgeryToken]` and emit the token in Razor forms.
[Back to top](#top)

### `languages/csharp/csharp-style.yml`
Goal: Promote idiomatic .NET code that stays readable across teams and tooling.

#### CSHARP-STYLE-001 - Apply `../../common/base.yml` guidance before enforcing C#-specific style rules.
- **Intent:** Base style keeps multi-language repos consistent before language flavour is added.
- **Example:** Ensure naming and formatting expectations from the base policy are satisfied before referencing C# specifics.
[Back to top](#top)

#### CSHARP-STYLE-002 - Follow .NET naming conventions for namespaces, types, and members.
- **Intent:** Consistent naming aids discoverability and tooling support.
- **Example:** Use `PascalCase` for classes and `camelCase` for fields rather than ad-hoc casing.
[Back to top](#top)

#### CSHARP-STYLE-003 - Use expression-bodied members only when the body remains trivial.
- **Intent:** Concise members improve clarity but multiline logic belongs in block bodies.
- **Example:** Keep getters expression-bodied while leaving complex validation in standard methods.
[Back to top](#top)

#### CSHARP-STYLE-004 - Place nullable reference annotations consistently across public APIs.
- **Intent:** Nullable annotations communicate intent to callers and tools.
- **Example:** Declare `string?` for optional parameters instead of disabling nullable warnings.
[Back to top](#top)

#### CSHARP-STYLE-005 - Prefer `var` when the inferred type is obvious and aids readability.
- **Intent:** `var` reduces redundancy without hiding meaning.
- **Example:** Use `var orderService = new OrderService()` but spell out anonymous projection types.
[Back to top](#top)

#### CSHARP-STYLE-006 - Organise usings with system directives first and remove unused imports.
- **Intent:** Ordered imports keep diffs tidy and avoid unnecessary references.
- **Example:** Enable dotnet format or IDE analyzers that group `System.*` before other namespaces.
[Back to top](#top)

### `languages/csharp/csharp-testing.yml`
Goal: Sustain reliable automated coverage for .NET services without sacrificing determinism.

#### CSHARP-TEST-001 - Follow `../../common/tdd_bdd.yml` before applying C#-specific testing practices.
- **Intent:** Shared testing philosophy grounds the suite before language details.
- **Example:** Start with behaviour-focused specs from the common guidance before selecting test doubles.
[Back to top](#top)

#### CSHARP-TEST-002 - Use xUnit or the team-standard framework for new tests.
- **Intent:** Standard frameworks streamline tooling and knowledge sharing.
- **Example:** Scaffold new unit tests with `dotnet new xunit` rather than mixing frameworks.
[Back to top](#top)

#### CSHARP-TEST-003 - Isolate external dependencies with test doubles or in-memory providers.
- **Intent:** Isolation keeps tests fast and predictable.
- **Example:** Substitute `IMailService` with a fake implementation when asserting notifications.
[Back to top](#top)

#### CSHARP-TEST-004 - Assert on behavioural outcomes rather than implementation details.
- **Intent:** Behaviour-focused assertions tolerate refactors while preserving intent.
- **Example:** Compare returned DTOs instead of verifying private field assignments.
[Back to top](#top)

#### CSHARP-TEST-005 - Apply data-driven tests for permutations instead of duplicating logic.
- **Intent:** Parameterised cases reduce duplication and highlight edge conditions.
- **Example:** Use `[Theory]` with `[InlineData]` to cover multiple validation paths in one test.
[Back to top](#top)

#### CSHARP-TEST-006 - Keep unit tests deterministic and independent of system time or locale.
- **Intent:** Deterministic tests prevent flaky pipelines and regional surprises.
- **Example:** Inject a clock abstraction rather than calling `DateTime.Now` inside assertions.
[Back to top](#top)

## 3. Applying These Guidelines
Load the relevant YAML policies into prompts and refer back here when debating architecture, reviewing code, or coaching new team members on C# expectations.
[Back to top](#top)
