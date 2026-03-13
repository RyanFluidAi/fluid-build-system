# Agent Frontmatter Field Reference

Complete specification for `.claude/agents/<name>.md` frontmatter fields.

## Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Unique identifier (lowercase, hyphens only) |
| `description` | string | What the agent does AND when to delegate to it. Claude uses this to decide when to spawn the agent automatically. |

## Optional Fields

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `model` | `haiku` \| `sonnet` \| `opus` \| `inherit` | `inherit` | LLM model to use. `inherit` uses the parent conversation's model. |
| `tools` | comma-separated string | inherits parent | Tools available to the agent. E.g. `Read, Glob, Grep, Bash` |
| `disallowedTools` | comma-separated string | none | Tools to explicitly deny. Useful when inheriting a broad toolset but want to restrict specific tools. |
| `permissionMode` | enum | `default` | Permission behavior (see below) |
| `maxTurns` | integer | none | Maximum agentic turns before the agent stops |
| `memory` | `user` \| `project` \| `local` | none | Persistent memory scope (see below) |
| `skills` | array of strings | none | Skill names to preload into the agent's system prompt |
| `isolation` | `worktree` | none | Runs agent in an isolated git worktree |
| `background` | boolean | `false` | Run as background task while main conversation continues |
| `mcpServers` | object or array | none | MCP server references |
| `hooks` | object | none | Agent-specific lifecycle hooks |

## Permission Modes

| Mode | Behavior |
|------|----------|
| `default` | Standard permission prompts for risky actions |
| `acceptEdits` | Auto-approves file edits (Edit, Write), still prompts for Bash |
| `dontAsk` | Auto-denies permission prompts (only explicitly listed tools work) |
| `bypassPermissions` | Skips all permission checks (use with extreme caution) |
| `plan` | Read-only exploration mode, cannot modify files |

## Memory Scopes

| Scope | Location | Shared | Use case |
|-------|----------|--------|----------|
| `user` | `~/.claude/agent-memory/<name>/` | No (personal) | Cross-project learnings |
| `project` | `.claude/agent-memory/<name>/` | Yes (version controlled) | Project-specific patterns |
| `local` | `.claude/agent-memory-local/<name>/` | No (gitignored) | Personal project notes |

When memory is enabled, the agent gets a `MEMORY.md` file (first 200 lines loaded into context) and can maintain topic-specific files alongside it.

## Task Tool (Subagent Delegation)

Agents can spawn other agents if `Task` is in their tools list:

- `Task` — can spawn any subagent
- `Task(verifier, test-runner)` — can only spawn named subagents

Note: subagents cannot spawn further subagents (no nesting). Use skills or chain from the main conversation for multi-level delegation.

## Agent-Specific Hooks

Agents can define their own hooks that run only while the agent is active:

```yaml
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/validate-command.sh"
  PostToolUse:
    - matcher: "Edit|Write"
      hooks:
        - type: command
          command: "./scripts/run-linter.sh"
  Stop:
    - hooks:
      - type: command
        command: "./scripts/cleanup.sh"
```

## Common Agent Patterns

**Read-only validator:**
```yaml
tools: Read, Glob, Grep, Bash
disallowedTools: Edit, Write
permissionMode: default
```

**Code fixer:**
```yaml
tools: Read, Glob, Grep, Bash, Edit, Write
permissionMode: acceptEdits
```

**Research agent:**
```yaml
tools: Read, Glob, Grep, WebSearch, WebFetch
permissionMode: plan
```

**Orchestrator:**
```yaml
tools: Read, Glob, Grep, Edit, Write, Task(worker-a, worker-b)
permissionMode: acceptEdits
```

**Background worker:**
```yaml
tools: Read, Glob, Grep, Bash, Edit, Write
permissionMode: acceptEdits
background: true
isolation: worktree
```
