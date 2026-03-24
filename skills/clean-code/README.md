# Clean Code - Uncle Bob's Principles for Claude Code

Apply Robert C. Martin's Clean Code and Clean Architecture principles to every piece of code Claude writes, reviews, or refactors.

**Approximate token count:** ~4,800 tokens (SKILL.md + 3 reference files)

## Features

- **Passive mode** (always active): expressive names, focused functions, explicit error handling, real DRY, no useless comments
- **Purist mode** (`/clean-code`): deep diagnostics with structured reports and violation scoring
- 6 subcommands: audit, init, refacto, review, prod, help
- Full SOLID principles reference
- Clean Architecture layers and Dependency Rule
- Actionable checklists for each workflow

## Installation

```bash
claude skills add PierreRtec/claude-skills-public@clean-code
```

## Usage

Passive mode activates automatically. For explicit audits:

```
> /clean-code              # Audit current context
> /clean-code init         # Structure a new project
> /clean-code refacto      # Analyze and plan refactoring
> /clean-code review       # PR-style review with scoring
> /clean-code prod         # Pre-production checklist
> /clean-code help         # Cheat sheet
```

### Example output

```
## Clean Code Diagnostic

### What's good
- Clear domain naming in the users/ module
- Good separation between API layer and business logic

### Violations
| # | File:line | Violated principle | Severity | Description |
|---|---|---|---|---|
| 1 | src/orders/process.ts:42 | SRP | Critical | Function handles validation AND payment for two different actors |
| 2 | src/utils/helpers.ts:15 | Naming | Minor | `data` says nothing, rename to `orderPayload` |

### Recommended actions
1. Extract `validateOrder()` and `processPayment()` from `processOrder()`
2. Rename `data` to `orderPayload` in helpers.ts
```

## File Structure

```
clean-code/
├── SKILL.md                                    # Main skill definition
├── README.md                                   # This file
└── references/
    ├── clean-code-principles.md                # Naming, functions, comments, errors, tests, smells
    ├── clean-architecture-principles.md         # SOLID, Dependency Rule, boundaries, components
    └── checklists.md                           # Checklists per subcommand
```

## License

MIT
