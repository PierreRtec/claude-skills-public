# Hooks

Deterministic guardrails around a non-deterministic agent.

A skill is a prompt : the model *should* follow it. A hook is code : the runtime *will* run it. When the cost of a mistake is irreversible (a force-push, a hard reset), a prompt is the wrong tool. Put the rule where the model cannot argue with it.

These are the two hooks worth stealing from my setup.

| Hook | Event | What it does |
|---|---|---|
| [destructive-command-guard](#destructive-command-guard) | `PreToolUse` on `Bash` | Blocks irreversible git and filesystem commands before they run |
| [mcp-failure-logger](#mcp-failure-logger) | `PostToolUseFailure` on `mcp__*` | Appends every MCP tool failure to a JSONL file for later analysis |

## destructive-command-guard

Exit code `2` on a `PreToolUse` hook cancels the tool call and feeds `stderr` back to the model as an error. So the model never runs the command, sees why, and adapts.

Covers the three that cannot be undone from the local repo : `git push --force`, `git reset --hard`, `rm -rf /`.

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "node -e \"const c=JSON.parse(process.env.CLAUDE_TOOL_INPUT||'{}').command||''; if(/(git push.*--force|git reset --hard|rm -rf \\/)/.test(c)){console.error('BLOCKED: destructive command detected');process.exit(2)}\""
          }
        ]
      }
    ]
  }
}
```

Add to `~/.claude/settings.json` (all projects) or `.claude/settings.json` (one project).

Notes :

- `--force-with-lease` is deliberately **not** blocked. It fails safely when the remote moved, which is the point.
- Widen the regex to your own footguns : `drop table`, `terraform destroy`, `kubectl delete namespace`, `DELETE FROM` without a `WHERE`.
- This complements, not replaces, the permission system. Permissions ask ; this refuses.

## mcp-failure-logger

MCP servers fail quietly : a 401 here, a 429 there, and you only notice when a workflow silently returns nothing. This appends one JSON object per failure so the pattern becomes visible.

```json
{
  "hooks": {
    "PostToolUseFailure": [
      {
        "matcher": "mcp__.*",
        "hooks": [
          {
            "type": "command",
            "command": "bash -c 'echo \"{\\\"ts\\\":\\\"$(date -Iseconds)\\\",\\\"event\\\":\\\"mcp_failure\\\",\\\"tool\\\":\\\"$CLAUDE_TOOL_NAME\\\"}\" >> ~/.claude/mcp-failures.jsonl'"
          }
        ]
      }
    ]
  }
}
```

Read it back :

```bash
# which MCP tool fails the most
jq -r .tool ~/.claude/mcp-failures.jsonl | sort | uniq -c | sort -rn
```

JSONL, not a log file : one object per line, appendable from a shell one-liner, greppable, and `jq`-able without a parser. Append-only means concurrent sessions never corrupt each other's writes.

## Why hooks instead of instructions

Three properties a prompt does not have :

1. **Non-negotiable.** No amount of context pressure makes a hook skip. An instruction in `CLAUDE.md` competes with everything else in the window.
2. **Zero token cost.** Guardrails that live in the runtime do not eat context. Rules in `CLAUDE.md` are paid for on every single request.
3. **Observable.** A hook can write to disk. That turns agent behaviour into data you can query afterwards, instead of a feeling about how the session went.

Rule of thumb : if breaking it costs an apology, write an instruction. If breaking it costs a rebuild, write a hook.

## Reference

[Claude Code hooks documentation](https://docs.anthropic.com/en/docs/claude-code/hooks)
