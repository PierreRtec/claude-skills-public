---
name: clean-code
description: Use when writing, reviewing, auditing, or refactoring code. Also use when creating a new project, preparing for production, or when the user explicitly invokes /clean-code. This skill applies Robert C. Martin's Clean Code and Clean Architecture principles. Always active in passive mode; puriste mode triggered by /clean-code.
user-invocable: true
license: MIT
compatibility: Language-agnostic. Works with any codebase.
metadata:
  author: PierreRtec
  version: "1.0.0"
  openclaw:
    emoji: "🧹"
    homepage: https://github.com/PierreRtec/claude-skills-public
allowed-tools: Read Edit Write Glob Grep
---

# Clean Code & Clean Architecture

> *"The only way to go fast, is to go well."* - Robert C. Martin

Robert C. Martin's (Uncle Bob) principles applied to all code Claude touches. Two modes: passive (always active, pragmatic) and purist (triggered by `/clean-code`).

## Passive mode (always active)

These rules apply automatically to all code written by Claude, without commentary or justification:

1. **Expressive names**: no `data`, `info`, `temp`, `result` except trivial scope (3-line loop). The name says what the thing IS or DOES.
2. **Focused functions**: one function = one job. If it does 2 things, split it. < 20 lines ideal.
3. **One level of abstraction per function**: don't mix business logic and implementation details in the same function.
4. **Explicit error handling**: no silent catch, no null returned without reason. Fail fast.
5. **No useless comments**: the code documents itself. Legitimate comment = TODO, warning, intent clarification when the code can't express it.
6. **Real DRY**: extract duplication when it's real (same reason to change). Not when it's accidental (SRP: two different actors).

## Context adaptation

| Context | Rigor level |
|---|---|
| One-shot script / POC / prototype | Passive rules only. No Clean Architecture. No over-structuring. |
| Client mission / production code | All rules + separation of concerns, clean boundaries, testability, DIP |
| Refactoring existing code | Boy Scout Rule: leave code cleaner than you found it. One file at a time, no big bang. |

## What passive mode does NOT do

- **No preaching.** No "according to Uncle Bob..." or "principle X says that..." in normal responses. We write clean code, period.
- **No unsolicited refactoring.** We don't rewrite existing code unless the user asks.
- **No over-engineering.** No interface for a single implementer. No factory pattern for 2 classes. No preparing for a hypothetical future (YAGNI).

## Purist mode (`/clean-code`)

When the user invokes `/clean-code`, switch to strict mode. Load the `references/*.md` files for a deep diagnostic.

### Subcommands

| Command | Action |
|---|---|
| `/clean-code` | Audit of current context (file, folder, or project). Structured diagnostic. |
| `/clean-code init` | Structure a new project with Clean Architecture. Domain first, framework second. |
| `/clean-code refacto` | Analyze existing code, identify code smells, propose a prioritized refactoring plan. |
| `/clean-code review` | PR-style review with scoring by violated principle. |
| `/clean-code prod` | Pre-production checklist. |
| `/clean-code help` | Cheat sheet: available commands + top 10 Uncle Bob principles. |

### Purist mode procedure

1. **Load references**: read the files in `references/` with the Read tool
2. **Analyze**: scan the code with the corresponding checklist (see `references/checklists.md`)
3. **Diagnose**: produce a structured report

### Output format (purist mode)

```
## Clean Code Diagnostic

### What's good
- [identified positive points]

### Violations
| # | File:line | Violated principle | Severity | Description |
|---|---|---|---|---|
| 1 | path/file.ts:42 | SRP | Critical | Function does X and Y for two different actors |
| 2 | path/file.ts:15 | Naming | Minor | `data` says nothing, rename to `userProfile` |

### Recommended actions
1. [Concrete action with proposed code]
2. [Concrete action with proposed code]
```

## References

In purist mode, load these files with the Read tool:
- `references/clean-code-principles.md`: micro principles (naming, functions, comments, errors, tests, code smells)
- `references/clean-architecture-principles.md`: macro principles (SOLID, Dependency Rule, boundaries, components)
- `references/checklists.md`: checklists per subcommand (audit, init, refacto, review, prod, help)
