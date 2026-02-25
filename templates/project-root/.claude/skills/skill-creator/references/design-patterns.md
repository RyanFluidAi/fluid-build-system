# Skill Design Patterns

## Pattern 1: High-level guide with references

Keep SKILL.md lean, link to detailed reference files:

```markdown
# API Integration
## Quick start
[minimal example]
## Advanced features
- **Authentication**: See [references/auth.md](references/auth.md)
- **Error handling**: See [references/errors.md](references/errors.md)
```

## Pattern 2: Domain-specific organization

For skills with multiple domains, organize by domain:

```
analytics-skill/
├── SKILL.md (overview + navigation)
└── references/
    ├── finance.md
    ├── sales.md
    └── product.md
```

Claude only loads the relevant reference file.

## Pattern 3: Conditional details

Show basic content, link to advanced:

```markdown
# Document Processing
## Basic editing
Modify the XML directly.
**For tracked changes**: See [references/tracked-changes.md]
**For advanced formatting**: See [references/formatting.md]
```

## Guidelines

- Keep references one level deep from SKILL.md
- For files >100 lines, include a table of contents
- Avoid deeply nested reference chains
