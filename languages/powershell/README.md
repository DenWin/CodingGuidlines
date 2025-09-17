<a id="top"></a>
# PowerShell Policy Rationale

## 1. About This README
This reference elaborates on the PowerShell policies that cover architecture, performance, security, style, and testing. Use it when coaching team members or clarifying expectations beyond the concise YAML rules, and lean on the examples to illustrate correct practice.
Treat the YAML files as the machine-readable source of truth. Use this README to educate humans, onboard new collaborators, or justify decisions during reviews. When adjusting rules, update both the YAML file and the relevant rationale entry to keep them aligned.

## 2. File-by-File Guidance
- [`languages/powershell/powershell-architecture.yml`](#languagespowershellpowershell-architectureyml)
  - [PS-ARCH-001 - Apply `../../common/architecture.yml` principles before PowerShell-specific architecture guidance.](#ps-arch-001---apply-commonarchitectureyml-principles-before-powershell-specific-architecture-guidance)
  - [PS-ARCH-002 - Package reusable code as modules instead of copying script blocks between files.](#ps-arch-002---package-reusable-code-as-modules-instead-of-copying-script-blocks-between-files)
  - [PS-ARCH-003 - Separate orchestration scripts from functions implementing core logic.](#ps-arch-003---separate-orchestration-scripts-from-functions-implementing-core-logic)
  - [PS-ARCH-004 - Use configuration data files (JSON, PSD1) rather than hardcoding environment values.](#ps-arch-004---use-configuration-data-files-json-psd1-rather-than-hardcoding-environment-values)
  - [PS-ARCH-005 - Design scripts to support non-interactive execution for automation.](#ps-arch-005---design-scripts-to-support-non-interactive-execution-for-automation)
  - [PS-ARCH-006 - Provide verbose and debug output switches for diagnostic purposes.](#ps-arch-006---provide-verbose-and-debug-output-switches-for-diagnostic-purposes)
- [`languages/powershell/powershell-performance.yml`](#languagespowershellpowershell-performanceyml)
  - [PS-PERF-001 - Apply `../../common/performance.yml` budgets and practices before PowerShell-specific optimisations.](#ps-perf-001---apply-commonperformanceyml-budgets-and-practices-before-powershell-specific-optimisations)
  - [PS-PERF-002 - Prefer pipeline operations over explicit loops when processing large datasets.](#ps-perf-002---prefer-pipeline-operations-over-explicit-loops-when-processing-large-datasets)
  - [PS-PERF-003 - Dispose disposable .NET objects with `using` or `try/finally` patterns.](#ps-perf-003---dispose-disposable-net-objects-with-using-or-tryfinally-patterns)
  - [PS-PERF-004 - Avoid unnecessary remote sessions; reuse PSSessions when possible.](#ps-perf-004---avoid-unnecessary-remote-sessions-reuse-pssessions-when-possible)
  - [PS-PERF-005 - Use background jobs or runspaces for parallel workloads when safe.](#ps-perf-005---use-background-jobs-or-runspaces-for-parallel-workloads-when-safe)
  - [PS-PERF-006 - Measure runtime with `Measure-Command` before and after optimisations.](#ps-perf-006---measure-runtime-with-measure-command-before-and-after-optimisations)
- [`languages/powershell/powershell-security.yml`](#languagespowershellpowershell-securityyml)
  - [PS-SEC-001 - Apply `../../common/compliance.yml` controls before PowerShell-specific security practices.](#ps-sec-001---apply-commoncomplianceyml-controls-before-powershell-specific-security-practices)
  - [PS-SEC-002 - Sign scripts intended for distribution or execution in production.](#ps-sec-002---sign-scripts-intended-for-distribution-or-execution-in-production)
  - [PS-SEC-003 - Avoid plaintext credentials; use `Get-Credential` or secret management modules.](#ps-sec-003---avoid-plaintext-credentials-use-get-credential-or-secret-management-modules)
  - [PS-SEC-004 - Validate all external input, including command-line arguments and pipeline data.](#ps-sec-004---validate-all-external-input-including-command-line-arguments-and-pipeline-data)
  - [PS-SEC-005 - Do not disable execution policy as a workaround for missing signatures.](#ps-sec-005---do-not-disable-execution-policy-as-a-workaround-for-missing-signatures)
  - [PS-SEC-006 - Log privileged operations with sufficient detail for auditing.](#ps-sec-006---log-privileged-operations-with-sufficient-detail-for-auditing)
- [`languages/powershell/powershell-style.yml`](#languagespowershellpowershell-styleyml)
  - [PS-STYLE-001 - Apply `../../common/base.yml` guidance before PowerShell-specific style conventions.](#ps-style-001---apply-commonbaseyml-guidance-before-powershell-specific-style-conventions)
  - [PS-STYLE-002 - Use PascalCase for functions and camelCase for variables.](#ps-style-002---use-pascalcase-for-functions-and-camelcase-for-variables)
  - [PS-STYLE-003 - Declare script parameters with `param()` blocks at the top of the file.](#ps-style-003---declare-script-parameters-with-param-blocks-at-the-top-of-the-file)
  - [PS-STYLE-004 - Prefer advanced functions with CmdletBinding for reusable modules.](#ps-style-004---prefer-advanced-functions-with-cmdletbinding-for-reusable-modules)
  - [PS-STYLE-005 - Avoid aliases in scripts; use full cmdlet names for clarity.](#ps-style-005---avoid-aliases-in-scripts-use-full-cmdlet-names-for-clarity)
  - [PS-STYLE-006 - Set `Set-StrictMode -Version Latest` when the script supports it.](#ps-style-006---set-set-strictmode--version-latest-when-the-script-supports-it)
- [`languages/powershell/powershell-testing.yml`](#languagespowershellpowershell-testingyml)
  - [PS-TEST-001 - Follow `../../common/tdd_bdd.yml` before applying PowerShell-specific testing practices.](#ps-test-001---follow-commontdd_bddyml-before-applying-powershell-specific-testing-practices)
  - [PS-TEST-002 - Use Pester for unit and integration testing of scripts and modules.](#ps-test-002---use-pester-for-unit-and-integration-testing-of-scripts-and-modules)
  - [PS-TEST-003 - Mock external commands and environment interactions in tests.](#ps-test-003---mock-external-commands-and-environment-interactions-in-tests)
  - [PS-TEST-004 - Name Pester tests using `Describe` and `It` blocks that explain behaviour.](#ps-test-004---name-pester-tests-using-describe-and-it-blocks-that-explain-behaviour)
  - [PS-TEST-005 - Keep tests idempotent and free from machine-specific dependencies.](#ps-test-005---keep-tests-idempotent-and-free-from-machine-specific-dependencies)
  - [PS-TEST-006 - Use `Should -Throw` and `Should -Not` assertions to cover error handling.](#ps-test-006---use-should--throw-and-should--not-assertions-to-cover-error-handling)

### `languages/powershell/powershell-architecture.yml`
Goal: Structure scripts and modules for reuse while staying automation-friendly across environments.

#### PS-ARCH-001 - Apply `../../common/architecture.yml` principles before PowerShell-specific architecture guidance.
- **Intent:** Shared architecture rules prevent automation from drifting away from broader standards.
- **Example:** Review common layering expectations before designing a new module layout.
[Back to top](#top)

#### PS-ARCH-002 - Package reusable code as modules instead of copying script blocks between files.
- **Intent:** Modules centralise logic and enable versioning.
- **Example:** Move shared helper functions into a `.psm1` module imported by deployment scripts.
[Back to top](#top)

#### PS-ARCH-003 - Separate orchestration scripts from functions implementing core logic.
- **Intent:** Orchestration should call well-tested functions, not contain business rules.
- **Example:** Keep `Deploy.ps1` focused on calling functions defined in `Deployment.psm1`.
[Back to top](#top)

#### PS-ARCH-004 - Use configuration data files (JSON, PSD1) rather than hardcoding environment values.
- **Intent:** Externalised config simplifies promotion across environments.
- **Example:** Load region settings from a PSD1 data file instead of embedding them in the script body.
[Back to top](#top)

#### PS-ARCH-005 - Design scripts to support non-interactive execution for automation.
- **Intent:** Automation should run unattended in CI/CD.
- **Example:** Replace `Read-Host` prompts with parameter inputs and defaults.
[Back to top](#top)

#### PS-ARCH-006 - Provide verbose and debug output switches for diagnostic purposes.
- **Intent:** Optional verbosity aids troubleshooting without cluttering normal runs.
- **Example:** Wrap diagnostic logging in `Write-Verbose` and `Write-Debug` calls.
[Back to top](#top)

### `languages/powershell/powershell-performance.yml`
Goal: Keep PowerShell automation responsive by leaning on pipelines, batching, and measurement.

#### PS-PERF-001 - Apply `../../common/performance.yml` budgets and practices before PowerShell-specific optimisations.
- **Intent:** Common budgets ensure improvements align with system-wide goals.
- **Example:** Check shared throughput targets before parallelising copy operations.
[Back to top](#top)

#### PS-PERF-002 - Prefer pipeline operations over explicit loops when processing large datasets.
- **Intent:** Pipelines reduce memory footprint and boost readability.
- **Example:** Replace `foreach` loops with `Get-Content ... | ForEach-Object` when streaming logs.
[Back to top](#top)

#### PS-PERF-003 - Dispose disposable .NET objects with `using` or `try/finally` patterns.
- **Intent:** Explicit disposal releases handles promptly.
- **Example:** Wrap `SqlConnection` usage in `using ([SqlConnection]::new(...)) { ... }`.
[Back to top](#top)

#### PS-PERF-004 - Avoid unnecessary remote sessions; reuse PSSessions when possible.
- **Intent:** Reusing sessions cuts connection overhead.
- **Example:** Create a single `New-PSSession` and pass it to multiple `Invoke-Command` calls.
[Back to top](#top)

#### PS-PERF-005 - Use background jobs or runspaces for parallel workloads when safe.
- **Intent:** Parallel execution shortens long-running jobs while isolating state.
- **Example:** Start PowerShell jobs to process independent servers instead of sequential loops.
[Back to top](#top)

#### PS-PERF-006 - Measure runtime with `Measure-Command` before and after optimisations.
- **Intent:** Measurement validates whether changes deliver gains.
- **Example:** Capture baseline runtimes with `Measure-Command` before refactoring a reporting script.
[Back to top](#top)

### `languages/powershell/powershell-security.yml`
Goal: Harden automation so credentials, policies, and auditing meet compliance expectations.

#### PS-SEC-001 - Apply `../../common/compliance.yml` controls before PowerShell-specific security practices.
- **Intent:** Compliance requirements frame scripting decisions.
- **Example:** Confirm retention rules before enabling transcript logging.
[Back to top](#top)

#### PS-SEC-002 - Sign scripts intended for distribution or execution in production.
- **Intent:** Signing assures provenance and supports execution policies.
- **Example:** Use organisation-issued code-signing certificates for modules deployed to servers.
[Back to top](#top)

#### PS-SEC-003 - Avoid plaintext credentials; use `Get-Credential` or secret management modules.
- **Intent:** Secure prompts and vaults prevent accidental leaks in source control.
- **Example:** Retrieve secrets from SecretManagement instead of storing passwords in scripts.
[Back to top](#top)

#### PS-SEC-004 - Validate all external input, including command-line arguments and pipeline data.
- **Intent:** Input validation blocks injection or misuse.
- **Example:** Verify that file paths supplied via parameters exist before acting on them.
[Back to top](#top)

#### PS-SEC-005 - Do not disable execution policy as a workaround for missing signatures.
- **Intent:** Execution policies protect environments from unsigned or tampered scripts.
- **Example:** Fix signing pipelines instead of running `Set-ExecutionPolicy Bypass` in production.
[Back to top](#top)

#### PS-SEC-006 - Log privileged operations with sufficient detail for auditing.
- **Intent:** Auditable logs support incident investigation.
- **Example:** Record account changes using `Write-Information` plus operation metadata when running admin scripts.
[Back to top](#top)

### `languages/powershell/powershell-style.yml`
Goal: Encourage idiomatic PowerShell that stays readable and tool-friendly across teams.

#### PS-STYLE-001 - Apply `../../common/base.yml` guidance before PowerShell-specific style conventions.
- **Intent:** Shared style keeps multi-language repositories aligned.
- **Example:** Ensure base requirements for naming and comments are met before citing PowerShell-specific rules.
[Back to top](#top)

#### PS-STYLE-002 - Use PascalCase for functions and camelCase for variables.
- **Intent:** Standard casing improves readability and aligns with PowerShell help.
- **Example:** Name a function `Get-ServerInventory` and a variable `$serverList`.
[Back to top](#top)

#### PS-STYLE-003 - Declare script parameters with `param()` blocks at the top of the file.
- **Intent:** Centralised parameters make scripts self-describing and easier to reuse.
- **Example:** Add a `param([string]$Environment)` block before any executable statements.
[Back to top](#top)

#### PS-STYLE-004 - Prefer advanced functions with CmdletBinding for reusable modules.
- **Intent:** Advanced functions expose cmdlet features like common parameters and pipeline binding.
- **Example:** Decorate reusable functions with `[CmdletBinding()]` and `Param()` attributes.
[Back to top](#top)

#### PS-STYLE-005 - Avoid aliases in scripts; use full cmdlet names for clarity.
- **Intent:** Full names aid discoverability and avoid alias collisions.
- **Example:** Use `Get-ChildItem` instead of `gci` in committed scripts.
[Back to top](#top)

#### PS-STYLE-006 - Set `Set-StrictMode -Version Latest` when the script supports it.
- **Intent:** Strict mode catches undeclared variables and subtle bugs.
- **Example:** Enable strict mode at script start and fix issues it surfaces rather than disabling it.
[Back to top](#top)

### `languages/powershell/powershell-testing.yml`
Goal: Ensure PowerShell validation stays deterministic, expressive, and CI-friendly.

#### PS-TEST-001 - Follow `../../common/tdd_bdd.yml` before applying PowerShell-specific testing practices.
- **Intent:** TDD/BDD principles focus tests on behaviour before tooling details.
- **Example:** Begin with a failing Pester test describing the desired script behaviour before coding.
[Back to top](#top)

#### PS-TEST-002 - Use Pester for unit and integration testing of scripts and modules.
- **Intent:** A single framework consolidates tooling and team knowledge.
- **Example:** Configure CI to run `Invoke-Pester` for each module change.
[Back to top](#top)

#### PS-TEST-003 - Mock external commands and environment interactions in tests.
- **Intent:** Mocks keep tests deterministic and safe.
- **Example:** Use `Mock` to substitute `Invoke-RestMethod` when testing API wrappers.
[Back to top](#top)

#### PS-TEST-004 - Name Pester tests using `Describe` and `It` blocks that explain behaviour.
- **Intent:** Clear naming communicates purpose and outcome.
- **Example:** Write `Describe 'Install-Module' { It 'installs when module missing' { ... } }`.
[Back to top](#top)

#### PS-TEST-005 - Keep tests idempotent and free from machine-specific dependencies.
- **Intent:** Idempotent tests avoid flakiness across agents.
- **Example:** Create temporary directories within the test run rather than touching system locations.
[Back to top](#top)

#### PS-TEST-006 - Use `Should -Throw` and `Should -Not` assertions to cover error handling.
- **Intent:** Explicit assertions guarantee scripts fail loudly when expected.
- **Example:** Assert that missing parameters trigger `Should -Throw` with the correct message.
[Back to top](#top)

## 3. Applying These Guidelines
When automating with PowerShell, load the relevant YAML files for machine guidance and consult this README to brief reviewers, authors, or stakeholders on the rationale behind the standards.
[Back to top](#top)
