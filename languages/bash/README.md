<a id="top"></a>
# Bash Policy Rationale

## 1. About This README
This README expands the Bash policies that guide architecture, performance, security, style, and testing. Consult it when you need broader explanations, practical examples, or onboarding material beyond the terse YAML rules.
Treat the YAML files as the machine-readable source of truth. Use this README to educate humans, onboard new collaborators, or justify decisions during reviews. When adjusting rules, update both the YAML file and the relevant rationale entry to keep them aligned.

## 2. File-by-File Guidance
- [`languages/bash/bash-architecture.yml`](#languagesbashbash-architectureyml)
  - [BASH-ARCH-001 - Apply `../../common/architecture.yml` principles before Bash-specific orchestration patterns.](#bash-arch-001---apply-commonarchitectureyml-principles-before-bash-specific-orchestration-patterns)
  - [BASH-ARCH-002 - Compose complex workflows from smaller scripts or functions to aid reuse.](#bash-arch-002---compose-complex-workflows-from-smaller-scripts-or-functions-to-aid-reuse)
  - [BASH-ARCH-003 - Separate configuration data from executable logic where feasible.](#bash-arch-003---separate-configuration-data-from-executable-logic-where-feasible)
  - [BASH-ARCH-004 - Use trap handlers to clean up resources on exit signals.](#bash-arch-004---use-trap-handlers-to-clean-up-resources-on-exit-signals)
  - [BASH-ARCH-005 - Design scripts to be idempotent when rerun with the same inputs.](#bash-arch-005---design-scripts-to-be-idempotent-when-rerun-with-the-same-inputs)
  - [BASH-ARCH-006 - Prefer declarative tooling (e.g. Makefiles) for orchestration when available.](#bash-arch-006---prefer-declarative-tooling-eg-makefiles-for-orchestration-when-available)
- [`languages/bash/bash-performance.yml`](#languagesbashbash-performanceyml)
  - [BASH-PERF-001 - Apply `../../common/performance.yml` budgets and practices before Bash-specific optimisations.](#bash-perf-001---apply-commonperformanceyml-budgets-and-practices-before-bash-specific-optimisations)
  - [BASH-PERF-002 - Prefer built-in shell operations over spawning subprocesses in loops.](#bash-perf-002---prefer-built-in-shell-operations-over-spawning-subprocesses-in-loops)
  - [BASH-PERF-003 - Avoid parsing large data sets with naive while-read loops without considering performance.](#bash-perf-003---avoid-parsing-large-data-sets-with-naive-while-read-loops-without-considering-performance)
  - [BASH-PERF-004 - Batch filesystem operations when manipulating many files.](#bash-perf-004---batch-filesystem-operations-when-manipulating-many-files)
  - [BASH-PERF-005 - Use `set -e` judiciously to prevent wasted work on failure.](#bash-perf-005---use-set--e-judiciously-to-prevent-wasted-work-on-failure)
  - [BASH-PERF-006 - Leverage parallel utilities like `xargs -P` when safe and beneficial.](#bash-perf-006---leverage-parallel-utilities-like-xargs--p-when-safe-and-beneficial)
- [`languages/bash/bash-security.yml`](#languagesbashbash-securityyml)
  - [BASH-SEC-001 - Apply `../../common/compliance.yml` controls before Bash-specific security practices.](#bash-sec-001---apply-commoncomplianceyml-controls-before-bash-specific-security-practices)
  - [BASH-SEC-002 - Validate user-supplied input before executing commands.](#bash-sec-002---validate-user-supplied-input-before-executing-commands)
  - [BASH-SEC-003 - Do not use eval unless there is no safer alternative.](#bash-sec-003---do-not-use-eval-unless-there-is-no-safer-alternative)
  - [BASH-SEC-004 - Use whitelists or allowlists when validating command arguments.](#bash-sec-004---use-whitelists-or-allowlists-when-validating-command-arguments)
  - [BASH-SEC-005 - Avoid sourcing files from writable directories at runtime.](#bash-sec-005---avoid-sourcing-files-from-writable-directories-at-runtime)
  - [BASH-SEC-006 - Drop privileges before executing dangerous operations when possible.](#bash-sec-006---drop-privileges-before-executing-dangerous-operations-when-possible)
- [`languages/bash/bash-style.yml`](#languagesbashbash-styleyml)
  - [BASH-STYLE-001 - Apply `../../common/base.yml` guidance before Bash-specific style conventions.](#bash-style-001---apply-commonbaseyml-guidance-before-bash-specific-style-conventions)
  - [BASH-STYLE-002 - Start scripts with `#!/usr/bin/env bash` and `set -euo pipefail` when safe.](#bash-style-002---start-scripts-with-usrbinenv-bash-and-set--euo-pipefail-when-safe)
  - [BASH-STYLE-003 - Quote variable expansions to avoid word splitting unless explicitly needed.](#bash-style-003---quote-variable-expansions-to-avoid-word-splitting-unless-explicitly-needed)
  - [BASH-STYLE-004 - Use functions to encapsulate logical units instead of long linear scripts.](#bash-style-004---use-functions-to-encapsulate-logical-units-instead-of-long-linear-scripts)
  - [BASH-STYLE-005 - Prefer `$(...)` command substitution over backticks.](#bash-style-005---prefer--command-substitution-over-backticks)
  - [BASH-STYLE-006 - Document script usage with a help function or comment header.](#bash-style-006---document-script-usage-with-a-help-function-or-comment-header)
- [`languages/bash/bash-testing.yml`](#languagesbashbash-testingyml)
  - [BASH-TEST-001 - Follow `../../common/tdd_bdd.yml` before applying Bash-specific testing practices.](#bash-test-001---follow-commontdd_bddyml-before-applying-bash-specific-testing-practices)
  - [BASH-TEST-002 - Write automated tests using Bats or the team-standard framework.](#bash-test-002---write-automated-tests-using-bats-or-the-team-standard-framework)
  - [BASH-TEST-003 - Mock external commands and environment variables in tests.](#bash-test-003---mock-external-commands-and-environment-variables-in-tests)
  - [BASH-TEST-004 - Verify error paths by asserting on exit codes and stderr output.](#bash-test-004---verify-error-paths-by-asserting-on-exit-codes-and-stderr-output)
  - [BASH-TEST-005 - Keep tests hermetic by avoiding reliance on global system state.](#bash-test-005---keep-tests-hermetic-by-avoiding-reliance-on-global-system-state)
  - [BASH-TEST-006 - Document test setup steps in the repository README or script header.](#bash-test-006---document-test-setup-steps-in-the-repository-readme-or-script-header)
### `languages/bash/bash-architecture.yml`
Goal: Keep shell orchestration modular, reusable, and resilient when scripts scale.

#### BASH-ARCH-001 - Apply `../../common/architecture.yml` principles before Bash-specific orchestration patterns.
- **Intent:** Ensure shell work inherits the shared architecture guardrails before custom tweaks.
- **Example:** Reference the common layering guidance before designing a Bash wrapper around a deployment pipeline.
[Back to top](#top)

#### BASH-ARCH-002 - Compose complex workflows from smaller scripts or functions to aid reuse.
- **Intent:** Smaller entry points are easier to test, reuse, and reason about during troubleshooting.
- **Example:** Split a monolithic provisioning script into `init.sh`, `configure.sh`, and `verify.sh` and orchestrate them from a lightweight driver.
[Back to top](#top)

#### BASH-ARCH-003 - Separate configuration data from executable logic where feasible.
- **Intent:** Configuration files can change without editing code, reducing regression risk.
- **Example:** Load environment-specific values from `.env` files rather than hard-coding them inside loops.
[Back to top](#top)

#### BASH-ARCH-004 - Use trap handlers to clean up resources on exit signals.
- **Intent:** Trap handlers prevent leaked temporary files or stuck locks when scripts fail early.
- **Example:** Register a `trap` that deletes a temp directory when the script receives `EXIT` or `INT` signals.
[Back to top](#top)

#### BASH-ARCH-005 - Design scripts to be idempotent when rerun with the same inputs.
- **Intent:** Idempotence keeps automation safe to retry after partial failure.
- **Example:** Check if a service account already exists before creating it to avoid duplicate entries when rerunning setup.
[Back to top](#top)

#### BASH-ARCH-006 - Prefer declarative tooling (e.g. Makefiles) for orchestration when available.
- **Intent:** Declarative runners simplify dependency ordering and parallel execution.
- **Example:** Move a series of `build.sh` calls into a `Makefile` so contributors can run `make deploy` instead of memorising script sequences.
[Back to top](#top)

### `languages/bash/bash-performance.yml`
Goal: Keep shell automation lean by favouring built-ins and batching expensive work.

#### BASH-PERF-001 - Apply `../../common/performance.yml` budgets and practices before Bash-specific optimisations.
- **Intent:** Baseline performance expectations should frame any shell-level tuning.
- **Example:** Confirm the service meets the shared latency targets before adding Bash-specific caching.
[Back to top](#top)

#### BASH-PERF-002 - Prefer built-in shell operations over spawning subprocesses in loops.
- **Intent:** Forking processes in tight loops slows scripts dramatically.
- **Example:** Use parameter expansion or `[[ ... ]]` tests instead of calling `sed` or `grep` repeatedly in a `for` loop.
[Back to top](#top)

#### BASH-PERF-003 - Avoid parsing large data sets with naive while-read loops without considering performance.
- **Intent:** Inefficient parsing burns CPU and can stall automation pipelines.
- **Example:** Swap a `while read` loop that parses a CSV for `awk` or `jq` when handling thousands of rows.
[Back to top](#top)

#### BASH-PERF-004 - Batch filesystem operations when manipulating many files.
- **Intent:** Batching reduces syscalls and improves throughput on large jobs.
- **Example:** Use `xargs` or `find -exec ... +` to delete files in groups instead of iterating one at a time.
[Back to top](#top)

#### BASH-PERF-005 - Use `set -e` judiciously to prevent wasted work on failure.
- **Intent:** Early exits keep machines from doing needless work after a hard failure.
- **Example:** Combine `set -e` with guarded subshells when a best-effort clean-up should still run.
[Back to top](#top)

#### BASH-PERF-006 - Leverage parallel utilities like `xargs -P` when safe and beneficial.
- **Intent:** Parallelism shortens long-running tasks while respecting resource limits.
- **Example:** Run package downloads in parallel with `xargs -P 4` once you confirm the target servers can handle the load.
[Back to top](#top)

### `languages/bash/bash-security.yml`
Goal: Harden shell automation so user input and environment access stay controlled.

#### BASH-SEC-001 - Apply `../../common/compliance.yml` controls before Bash-specific security practices.
- **Intent:** Shared compliance obligations should drive shell hardening choices.
- **Example:** Review confidentiality requirements before logging command output that may contain secrets.
[Back to top](#top)

#### BASH-SEC-002 - Validate user-supplied input before executing commands.
- **Intent:** Input validation blocks command injection and misuse.
- **Example:** Sanitize a filename argument with a regex before passing it to `rm`.
[Back to top](#top)

#### BASH-SEC-003 - Do not use eval unless there is no safer alternative.
- **Intent:** `eval` executes arbitrary text and is a common injection vector.
- **Example:** Prefer indirect expansion `${!var}` over `eval "$var"` when reading dynamic variable names.
[Back to top](#top)

#### BASH-SEC-004 - Use whitelists or allowlists when validating command arguments.
- **Intent:** Explicit allowlists constrain input to known-safe values.
- **Example:** Restrict a `mode` argument to `start|stop|status` before dispatching to functions.
[Back to top](#top)

#### BASH-SEC-005 - Avoid sourcing files from writable directories at runtime.
- **Intent:** Writable paths invite tampering that executes untrusted code.
- **Example:** Source helper scripts from `/usr/local/lib/project/` rather than `/tmp` or the working directory.
[Back to top](#top)

#### BASH-SEC-006 - Drop privileges before executing dangerous operations when possible.
- **Intent:** Running high-risk commands with minimal privileges limits blast radius.
- **Example:** Use `sudo -u deploy` to run deployment steps after completing root-only preparations.
[Back to top](#top)

### `languages/bash/bash-style.yml`
Goal: Keep Bash scripts predictable, readable, and aligned with the common baseline.

#### BASH-STYLE-001 - Apply `../../common/base.yml` guidance before Bash-specific style conventions.
- **Intent:** The shared style foundation keeps cross-language teams aligned.
- **Example:** Reconfirm naming and formatting rules from the base policy before tailoring Bash specifics.
[Back to top](#top)

#### BASH-STYLE-002 - Start scripts with `#!/usr/bin/env bash` and `set -euo pipefail` when safe.
- **Intent:** Standardised shebangs and shell options remove ambiguity about execution behaviour.
- **Example:** Begin deployment scripts with `#!/usr/bin/env bash` to ensure Bash features are available even on distros where `/bin/sh` is dash.
[Back to top](#top)

#### BASH-STYLE-003 - Quote variable expansions to avoid word splitting unless explicitly needed.
- **Intent:** Quoting prevents surprises with spaces or glob characters.
- **Example:** Wrap `"$file"` when iterating over file paths that may contain spaces.
[Back to top](#top)

#### BASH-STYLE-004 - Use functions to encapsulate logical units instead of long linear scripts.
- **Intent:** Functions make scripts easier to test and reuse.
- **Example:** Wrap log rotation steps in a `rotate_logs()` function rather than embedding the logic inline.
[Back to top](#top)

#### BASH-STYLE-005 - Prefer `$(...)` command substitution over backticks.
- **Intent:** Modern substitution is easier to nest and read.
- **Example:** Use `result=$(grep pattern file)` to capture output instead of legacy backticks.
[Back to top](#top)

#### BASH-STYLE-006 - Document script usage with a help function or comment header.
- **Intent:** Clear documentation keeps contributors aligned on flags and prerequisites.
- **Example:** Provide a `usage()` helper that prints supported options when users pass `-h`.
[Back to top](#top)

### `languages/bash/bash-testing.yml`
Goal: Ensure Bash automation remains testable, deterministic, and friendly to CI environments.

#### BASH-TEST-001 - Follow `../../common/tdd_bdd.yml` before applying Bash-specific testing practices.
- **Intent:** Common testing expectations frame shell-specific approaches.
- **Example:** Reference the shared BDD guidance before writing new Bats scenarios.
[Back to top](#top)

#### BASH-TEST-002 - Write automated tests using Bats or the team-standard framework.
- **Intent:** Automated tests catch regressions before scripts ship to production.
- **Example:** Add a Bats test that asserts a provisioning script exits with code 0 and prints the expected summary.
[Back to top](#top)

#### BASH-TEST-003 - Mock external commands and environment variables in tests.
- **Intent:** Isolation keeps tests deterministic and safe.
- **Example:** Override `curl` with a stub that returns canned JSON inside the test environment.
[Back to top](#top)

#### BASH-TEST-004 - Verify error paths by asserting on exit codes and stderr output.
- **Intent:** Error-path coverage ensures scripts fail loudly when something breaks.
- **Example:** Assert that missing configuration causes exit code 1 and an informative message on stderr.
[Back to top](#top)

#### BASH-TEST-005 - Keep tests hermetic by avoiding reliance on global system state.
- **Intent:** Hermetic tests avoid flakiness tied to host machines.
- **Example:** Use temporary directories and mock data instead of `/var/log` files during tests.
[Back to top](#top)

#### BASH-TEST-006 - Document test setup steps in the repository README or script header.
- **Intent:** Shared documentation helps contributors run the suite consistently.
- **Example:** Note required packages and commands at the top of the test README so new maintainers can install dependencies quickly.
[Back to top](#top)

## 3. Applying These Guidelines
Load the Bash YAML files into prompts when working on shell automation, and reference this README for human decision-making, code reviews, or onboarding materials.
[Back to top](#top)


