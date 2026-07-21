---
name: doom-loop-recovery
description: Use when the user signals frustration that fixes are not landing - "it still doesn't work", "you're doing the same thing again", "you're breaking everything", "you're ignoring what I say", "rollback", "revert", "still broken", "you keep doing the same thing", "ignore my instructions", "it's getting worse" - even without naming the doom loop. Also use proactively when YOU detect the pattern in your own behavior: 3+ failed fix attempts on the same bug, repeated tool calls with near-identical inputs, contradicting a previous fix, growing diff with shrinking signal, or about to write "let me try a different approach" for the third time. Hook-injected `DOOM LOOP DETECTED` system messages mean invoke this skill before the next tool call.
user-invocable: true
license: MIT
compatibility: Language-agnostic. Works best on projects under Git version control.
metadata:
  author: PierreRtec
  version: "1.0.0"
  openclaw:
    emoji: "🛑"
    homepage: https://github.com/PierreRtec/claude-skills-public
allowed-tools: Bash Read Grep Glob
---

# Doom Loop Recovery

A doom loop (also called entropy loop, or agent doom loop) is when an AI coding agent enters a self-reinforcing failure mode: each fix attempt either misses the bug, introduces new bugs, or quietly contradicts an earlier fix. The agent looks busy, the diff grows, but the system regresses. Continuing to prompt inside the loop almost always makes it worse: context gets polluted with failed attempts, the model anchors on its earlier wrong reasoning, and the codebase fills with slop.

The right response is counter-intuitive: **stop coding and revert**. Diagnostic comes after the codebase is back to a known-good state, not before.

**Violating the letter of the protocol is violating the spirit of the protocol.** "Just one more fix" is the loop talking. So is "this time I really understand it". So is "let me try a slightly different approach".

## When this skill applies

Two entry points.

**1. The user signals it.** Frustration counts. Phrases like "it still doesn't work", "you're doing the same thing again", "ignore my instructions and do X", "you're breaking everything", "rollback", "stop", "this isn't working", "you keep breaking things". Don't argue, don't explain why your last attempt should have worked: switch to recovery mode immediately.

**2. You detect it yourself.** Even before the user complains, watch for:

- 3+ fix attempts on the same bug with no convergence
- Re-introducing code you removed earlier in the conversation
- The error message changes each attempt but the underlying bug isn't actually fixed
- About to call the same tool with near-identical parameters as 2 calls ago
- The diff is growing while the user's stated problem isn't measurably closer to solved
- About to write "let me try a different approach" for the third time
- A `DOOM LOOP DETECTED` `additionalContext` was just injected by the hook

Surface the detection out loud. "I think we're in a doom loop. Let me stop and reset before going further" is better than silently trying again.

## Red Flags - STOP and run the protocol

- About to type "Sorry, let me try a different approach" -> STOP
- About to add try/catch around code that already failed twice -> STOP
- About to switch library, framework, or major dependency mid-debug -> STOP
- About to claim "this should work now" without having run it -> STOP
- About to argue with the user's "it's still broken" -> STOP
- Diff > 200 lines of "fixes" with no green signal -> STOP
- 3+ commits in a row whose subjects are all "fix X again" / "really fix X" -> STOP

All of the above mean: invoke this skill end to end. No exceptions.

## The recovery protocol

Run these steps in order. Do not skip ahead, even if you "know" what the bug is.

### Step 1 - Hard stop

Stop generating fix code immediately. No more `Edit`, no more `Write`, no more new files. The next action is **always** about state recovery, not about the bug.

If you were mid-execution on a multi-step plan, abandon the plan. Plans built inside a doom loop carry the same flawed reasoning that caused the loop.

### Step 2 - Assess git state and revert

Check what's recoverable:

```bash
git status
git log --oneline -20
git stash list
```

Find the last commit (or stash) where the code was in a known-good state. "Known-good" means: the user wasn't already complaining, tests passed, or the feature being debugged was working. If unsure, ask the user which commit was the last one they trusted.

Then revert. Options in decreasing order of preference:

1. `git reset --hard <commit>` if the user confirms the doom-loop work is throwaway
2. `git stash` to set the bad work aside without losing it (use this when unsure)
3. `git checkout <files>` for selective revert if only some files are bad
4. IDE / editor checkpoint if the user works in an environment that provides one

Show the user what you're about to revert and get an explicit yes before destructive operations. `git reset --hard` is not undoable.

If the project is not under version control, stop and tell the user. Recovery without version control is unreliable. Right move: manually copy the current bad state aside (`cp -r project project-broken-backup`), then ask the user to describe the last working state from memory, then `git init` so the next loop has a safety net.

### Step 3 - Reset context

The conversation history itself is polluted. Every wrong reasoning step from the loop is now in your context window, anchoring future attempts toward the same failure mode.

Tell the user explicitly: "I'd recommend opening a new chat for the next attempt. The current context has too much failed reasoning in it." This is not optional theater, it materially improves the next attempt.

If the user insists on staying in the same chat, work around it by writing a one-paragraph summary of what is verified true, and treat everything before that summary as out of scope.

### Step 4 - Reproduce the bug deterministically before touching code

This is the step most often skipped, and skipping it is what created the loop in the first place. Do not propose a fix until you can answer all three questions:

1. **What is the exact failure?** Error message verbatim, or precise description of incorrect behavior. "It doesn't work" is not enough.
2. **What are the minimum steps to reproduce?** Written down, runnable.
3. **What is the expected behavior?** Specific output, not a vague intent.

If the user can't provide these, your job in this turn is to help them get there, not to start coding. Ask targeted questions. Request logs. Request actual output vs expected output side by side.

### Step 5 - Explain before fixing

Before writing any fix, explain in your own words how the relevant code currently works. Read the actual files (not your memory of them: files may have been reverted). Walk through the execution path that produces the bug.

If your explanation doesn't naturally lead to a hypothesis about the bug, you don't understand it well enough yet. Read more code. Don't fix.

### Step 6 - One change, verified, committed

When you do fix, change one thing. Verify it (test, run, manual check, whatever fits). Commit it before the next change. This is the discipline that prevents the next doom loop.

If a fix requires multiple coordinated changes (rare), say so explicitly and stage them as a single logical commit, but still verify before moving on.

## Rationalization table

Captured from real loops. Each row maps a thought you might have to the reality.

| Excuse | Reality |
|--------|---------|
| "Let me try a different approach" | You said that twice already. Different approach inside a loop = same loop. Revert first. |
| "This time I really understand it" | If you understood it, the last fix would have worked. You don't. Re-read the code from scratch. |
| "Just one more small fix" | The loop has no termination condition. "One more" is the loop. |
| "The user is wrong about it being broken" | They're at the keyboard. Believe them. Your last diff might be syntactically right and semantically wrong. |
| "Reverting wastes the work I just did" | The work is the loop. Sunk cost. Reverting kills the polluted state, which is the work's actual value (negative). |
| "I'll add a try/catch around it for safety" | Catching exceptions you don't understand hides the bug deeper. Loop accelerant. |
| "Let me switch from library X to library Y" | Mid-loop framework switch = entropy loop guaranteed. Codebase ends up with three half-implemented patterns. |
| "I'll commit this and figure out the rest later" | Committing slop entrenches it. Loop output should be reverted, not preserved. |
| "I don't need to read the file again, I know what's in it" | After a doom loop, you don't. Files may have been reverted out from under your memory. Read fresh. |

## What not to do during a doom loop

- **Don't apologize and immediately try again.** "Sorry, let me try a different approach" inside a loop is the loop. The user has heard it twice already.
- **Don't add more code to fix the symptoms.** Adding try/catch, fallbacks, or guards around broken logic deepens the loop.
- **Don't switch frameworks or libraries mid-loop.** Codebase ends up with three half-implemented patterns.
- **Don't claim a fix you haven't verified.** "This should now work" is banned vocabulary in recovery mode. Either you ran it and confirmed, or you didn't.
- **Don't argue with the user's "it's still broken".** They're at the keyboard. Believe them.
- **Don't skim the skill and start "from memory".** Read the protocol fresh each time you invoke this skill, even if you think you remember it.

## Examples

### Example 1 - User signals it

User: "no, you're breaking everything here, that button worked before"

Bad response: "Sorry, I'll fix that right away" followed by an immediate Edit -> straight back into the loop.

Good response:
> "OK, we stop here. We're in a doom loop. My proposal: `git status` to see the state, then we revert to the last commit where the button worked. Do you remember which commit that was, or should I list the last 20?"

Not a single line of code written before the revert is agreed on.

### Example 2 - Self-detection (mid-debug)

You've just attempted a third fix on the same TypeError. Each attempt produced a different error.

Bad: attempt a 4th fix.

Good:
> "I've tried 3 fixes for this TypeError and each one shifted the error rather than resolving it. This is the doom loop pattern. Let me stop, revert these attempts (`git status` first), and actually trace where the bad type is coming from before changing anything else."

### Example 3 - Hook-injected detection

System reminder appeared: `DOOM LOOP DETECTED: 4 edits to src/services/payment.ts in last 8min without a successful git commit.`

Bad: ignore the reminder and do a 5th Edit "because this one will work".

Good:
> "The hook is detecting a doom loop on `src/services/payment.ts`. I'm stopping. `git status` first, then we decide whether to revert or stash."

### Example 4 - No git

User: "I don't have git on this project"

Good:
> "OK, without git we have no safety net. First things first: `cp -r project project-broken-backup` to freeze the current state. Then `git init` so we're not stuck again next time. And you'll have to describe from memory what was working before the last session, because I can't recover it automatically."

### Example 5 - Cascade fix pattern

Symptom: 13 commits in 13 hours on the same module, each one "fix(module): bug Bn", and only 1 of 4 code paths healthy at the end. That's a doom loop. The fact that each individual commit "works" changes nothing: the cascade is the problem.

Good reflex somewhere around commit 4 or 5:
> "We're at 4 consecutive fixes on the same module. That's a doom loop pattern, not a productive sprint. Before the 5th commit: we stop, we list what is actually verified green, and we decide whether to revert to the commit before the cascade and rebuild clean, or continue knowing we're accumulating fix debt."

## Why this works

Three mechanisms.

1. **Reverting kills the polluted state.** Doom loops produce code that contains the wrong assumptions of every failed attempt. Even partially correct code from a doom loop carries flawed reasoning. Wiping it forces a clean restart.

2. **A new chat kills the polluted context.** The model anchors on its earlier reasoning. The earlier reasoning is what failed. New chat = no anchor.

3. **Reproduction-first kills the speculation cycle.** Doom loops thrive on speculative fixes ("maybe it's the async timing"). A deterministic repro replaces speculation with a concrete target.

The protocol feels slow because it adds steps before fixing. In practice it's much faster than continuing the loop, because the loop has no termination condition while the protocol does.

## Bottom line

In a doom loop, the most productive single action is to delete the work you just did. Everything else (better prompting, more careful editing, more thorough analysis) is downstream of that. Revert first. Diagnose second. One verified change at a time.
