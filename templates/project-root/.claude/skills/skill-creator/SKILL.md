---
name: skill-creator
description: Guide for creating effective skills that extend Claude's capabilities with specialized knowledge, workflows, or tool integrations. Use when creating a new skill or updating an existing one.
argument-hint: "[skill name or domain]"
---

# Skill Creator

## About Skills

Skills are modular, self-contained packages that extend Claude's capabilities by providing specialized knowledge, workflows, and tools. They transform Claude from a general-purpose agent into a specialized one equipped with procedural knowledge.

## Core Principles

### Concise is Key

The context window is a shared resource. Only add context Claude doesn't already have. Prefer concise examples over verbose explanations. Keep SKILL.md under 500 lines.

### Set Appropriate Degrees of Freedom

- **High freedom** (text instructions): when multiple approaches are valid
- **Medium freedom** (pseudocode/scripts with parameters): when a preferred pattern exists
- **Low freedom** (specific scripts, few parameters): when operations are fragile

### Skill Directory Structure

```
skill-name/
├── SKILL.md (required)
│   ├── YAML frontmatter (name, description required)
│   └── Markdown instructions
└── Optional resources
    ├── scripts/       — executable code for deterministic tasks
    ├── references/    — documentation loaded on demand
    └── assets/        — files used in output (templates, etc.)
```

## SKILL.md Frontmatter

Required fields:
- `name`: skill identifier (lowercase, hyphens only)
- `description`: what the skill does AND when to use it — this is the primary trigger mechanism

Optional fields:
- `argument-hint`: hint text in autocomplete, e.g. `[filename]`
- `disable-model-invocation`: `true` = only user can invoke via `/name`
- `user-invocable`: `false` = hidden from `/` menu, Claude loads automatically when relevant
- `context`: `fork` = runs in isolated subagent context
- `agent`: which subagent type when `context: fork` (e.g. `general-purpose`, `Explore`)
- `allowed-tools`: auto-approve specific tools when this skill is active

## Creation Process

1. **Understand the skill** — gather concrete examples of how it will be used
2. **Plan reusable contents** — identify scripts, references, and assets needed
3. **Create the directory** — `mkdir -p .claude/skills/<skill-name>`
4. **Write SKILL.md** — frontmatter + instructions
5. **Add resources** — scripts, references, assets as needed
6. **Test** — invoke the skill on real tasks and iterate

## Progressive Disclosure

- **Level 1 (always in context)**: name + description (~100 words)
- **Level 2 (when invoked)**: SKILL.md body (<500 lines)
- **Level 3 (on demand)**: reference files, scripts (unlimited)

Reference supporting files from SKILL.md so Claude knows they exist and when to read them.

## What NOT to Include

- README.md, CHANGELOG.md, or other meta-documentation
- Setup/testing procedures
- User-facing documentation
- Anything Claude already knows (general programming concepts)

See [references/](references/) for detailed design patterns and output format guidance.
