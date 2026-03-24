# Clean Code Checklists

Checklists per subcommand. Each item is actionable and verifiable.

## Audit Checklist (`/clean-code`)

General audit of current code. Scan each item and report violations.

- [ ] Functions > 30 lines? Split them.
- [ ] Vague names (`data`, `handler`, `manager`, `utils`, `helper`)? Rename with intention.
- [ ] Comments that paraphrase the code? Delete them.
- [ ] try/catch that swallow errors silently? Fix.
- [ ] Dead or commented-out code? Delete (git keeps it).
- [ ] Real duplication (same reason to change) at 3+ places? Extract.
- [ ] File > 300 lines? Probably multiple responsibilities. Identify the actors.
- [ ] Unused imports? Clean up.
- [ ] Hidden coupling (globals, singletons, side effects)? Make explicit via injection.
- [ ] Tests exist? Sufficient coverage of critical and edge cases?
- [ ] Functions with > 3 arguments? Extract a parameter object.
- [ ] Boolean as function argument (flag argument)? Split into 2 functions.
- [ ] Mixed abstraction levels in the same function? Extract.
- [ ] null returned or passed as argument without reason? Fix.
- [ ] Dependencies in the wrong direction (business rules importing framework)? Invert (DIP).

## Init Checklist (`/clean-code init`)

Structure a new project according to Clean Architecture.

- [ ] Define the business domain in 1 clear sentence
- [ ] Structure by feature/domain, NOT by technical layer
  ```
  # Good
  src/users/  src/invoices/  src/payments/
  # Bad
  src/controllers/  src/services/  src/models/
  ```
- [ ] Separate: entities, use cases, adapters, infrastructure
- [ ] Define interfaces/contracts BEFORE implementations
- [ ] The project structure must scream its domain (Screaming Architecture)
- [ ] Setup tests from the start (framework + first passing test)
- [ ] Configure linter + formatter (consistency from day 1)
- [ ] Clean .gitignore (no node_modules, .env, build artifacts)
- [ ] Minimal README: what the project does, how to run it, chosen architecture
- [ ] Minimal CI: at least lint + tests on each push
- [ ] The framework is a detail: the core must not depend on it

## Refacto Checklist (`/clean-code refacto`)

Analysis and prioritized refactoring plan.

### Phase 1: Diagnosis
- [ ] Identify code smells by severity
  - **Critical**: rigidity (change cascades), fragility (unrelated breakage), tight coupling
  - **Medium**: long functions, vague naming, duplication
  - **Minor**: formatting, useless comments, dead imports
- [ ] List most-modified files (hotspots): that's where refactoring has the most impact

### Phase 2: Plan
- [ ] Prioritize: first what blocks/slows the most
- [ ] One refactor = one commit. No big bang.
- [ ] Each step must keep tests green
- [ ] Do NOT change behavior during refactoring

### Phase 3: Execution
- [ ] Boy Scout Rule: each file touched comes out cleaner
- [ ] Order: rename > extract functions > extract classes > extract modules
- [ ] Verify tests pass after EACH step
- [ ] If no tests: write them BEFORE refactoring (characterization tests)

## Review Checklist (`/clean-code review`)

PR-style review with scoring.

### Naming
- [ ] Each variable/function/class has a name that reveals its intention?
- [ ] No generic names (`data`, `info`, `temp`, `result`) outside trivial scope?
- [ ] Consistent naming convention across all code?

### Functions
- [ ] Functions < 30 lines?
- [ ] Each function does ONE thing?
- [ ] No flag arguments (boolean params)?
- [ ] < 3 arguments per function?
- [ ] No hidden side effects?

### Architecture
- [ ] SRP respected (one file/module = one responsibility, one actor)?
- [ ] Dependencies in the right direction (DIP)?
- [ ] No coupling to implementation details (DB, framework)?
- [ ] Interfaces used for external dependencies?

### Errors & Robustness
- [ ] Explicit error handling (no empty catch)?
- [ ] No null returned without reason?
- [ ] Fail fast on invalid inputs?

### Tests
- [ ] Tests present and relevant?
- [ ] Tests independent (no order required)?
- [ ] Descriptive test names (scenario + expected result)?

### Scoring

| Severity | Description | Impact |
|---|---|---|
| **Critical** | SOLID violation, tight coupling, no error handling | Blocks the review |
| **Medium** | Long functions, vague naming, duplication | Fix before merge |
| **Minor** | Formatting, comments, optimizations | Suggestion |

Final score: number of violations by severity. 0 critical = mergeable.

## Prod Checklist (`/clean-code prod`)

Pre-production. Everything must be green.

### Hygiene
- [ ] No `console.log` / `print` debug statements remaining
- [ ] No commented-out code
- [ ] No critical TODOs remaining (minor TODOs are OK)
- [ ] Imports cleaned up

### Errors & Logging
- [ ] Complete error handling (no empty catch)
- [ ] Structured logging in place (not console.log, a real logger)
- [ ] Useful error messages (not "Error occurred")
- [ ] Errors properly propagated (not swallowed)

### Security
- [ ] Environment variables externalized (no hardcoded secrets)
- [ ] Input validation at system boundaries (API, forms)
- [ ] No injection possible (SQL, XSS, command)
- [ ] No sensitive data in logs

### Architecture
- [ ] Clean boundaries (DB, external APIs behind interfaces)
- [ ] No circular dependencies
- [ ] Core doesn't depend on the framework

### Performance
- [ ] No N+1 queries
- [ ] No unnecessary loops on large collections
- [ ] No redundant computations in loops

### Tests
- [ ] Critical tests pass (happy path + main edge cases)
- [ ] Non-regression tests for fixed bugs
- [ ] CI green

## Help Cheat Sheet (`/clean-code help`)

### Commands

| Command | Description |
|---|---|
| `/clean-code` | Audit of current context |
| `/clean-code init` | Structure new project with Clean Architecture |
| `/clean-code refacto` | Analysis + prioritized refactoring plan |
| `/clean-code review` | PR review with scoring |
| `/clean-code prod` | Pre-production checklist |
| `/clean-code help` | This help |

### Top 10 Uncle Bob Principles

1. **"The only way to go fast, is to go well."**
2. **Intention-revealing names**: the name says what the thing IS or DOES
3. **Short functions**: < 20 lines, one job
4. **SRP**: one module = one actor, one reason to change
5. **DIP**: depend on abstractions, not concretions
6. **Dependency Rule**: dependencies point inward (business rules)
7. **No useless comments**: the code documents itself
8. **Fail fast**: fail early and clearly
9. **YAGNI**: don't code for the hypothetical future
10. **Boy Scout Rule**: leave code cleaner than you found it

> *"Clean code reads like well-written prose."* - Robert C. Martin
