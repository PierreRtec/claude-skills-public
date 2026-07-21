# Doom Loop Recovery - Stop the Loop, Revert, Diagnose

A doom loop is when a coding agent keeps "fixing" the same bug while the diff grows and the system regresses. This skill detects the pattern (from user frustration or from the agent's own behavior) and runs a strict 6-step recovery protocol: hard stop, revert to a known-good state, reset context, reproduce deterministically, explain before fixing, then one verified change at a time.

**Approximate token count:** ~2,400 tokens (SKILL.md)

## Features

- **Dual trigger**: fires on user frustration signals ("still broken", "you keep breaking things", "rollback") and on self-detected patterns (3+ failed fixes, near-identical tool calls, growing diff with no green signal)
- **6-step recovery protocol**: hard stop -> git assess & revert -> context reset -> deterministic repro -> explain before fixing -> one verified change per commit
- **Red flags checklist**: the exact moments that mean STOP (about to add try/catch to code that already failed twice, about to switch libraries mid-debug, about to claim "this should work now" unverified)
- **Rationalization table**: 9 excuses the loop makes you say, each mapped to the reality
- **Hook-friendly**: recognizes injected `DOOM LOOP DETECTED` system messages as a mandatory invocation trigger
- **No-git fallback**: backup, `git init`, and a memory-based reconstruction path
- Escape hatch: `/doom-loop` to force invocation when you spot it before the agent does

## Installation

```bash
claude skills add PierreRtec/claude-skills-public@doom-loop-recovery
```

## Usage

The skill triggers on its own, but you can force it:

```
> /doom-loop
```

Or just say what you're seeing:

```
> it's still broken and you keep doing the same thing
```

### Example output

```
I think we're in a doom loop. I've tried 3 fixes for this TypeError and each
one shifted the error instead of resolving it. Stopping here.

Current state:
  git status      -> 6 modified files, no commit since 14:02
  git log         -> last green commit is a1b2c3d "feat: add payment retry"

Proposal: stash the current work (recoverable) and reset to a1b2c3d.
Confirm before I run anything destructive.

After the revert, I won't propose a fix until I can state:
  1. the exact error, verbatim
  2. minimal runnable repro steps
  3. the expected behavior
```

## Why it matters

Prompting harder inside a doom loop is the single most expensive failure mode in agentic coding: the context fills with failed reasoning that anchors every subsequent attempt, and the codebase fills with half-applied fixes that contradict each other. The loop has no termination condition. This protocol does.

## License

MIT
