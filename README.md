# Claude Skills

<p align="center">
  <a href="https://github.com/slnquangtran/claude-skills/releases"><img src="https://img.shields.io/github/v/release/slnquangtran/claude-skills?style=for-the-badge&color=blue" alt="GitHub Release"></a>
  <img src="https://img.shields.io/badge/skills-1-green?style=for-the-badge" alt="Skills">
  <a href="https://github.com/slnquangtran/claude-skills/blob/main/LICENSE"><img src="https://img.shields.io/github/license/slnquangtran/claude-skills?style=for-the-badge&color=green" alt="License"></a>
</p>

My custom AI skills collection for Claude Code and other AI coding assistants.

## Available Skills

| Skill | Description | Version |
|-------|-------------|---------|
| **[PDF Master](/.claude/skills/pdf-master)** | PDF editing, generation, and manipulation intelligence | 1.0.0 |

## Installation

### Claude Code

Copy the skill folder to your project:

```bash
# Clone this repo
git clone https://github.com/slnquangtran/claude-skills.git

# Copy skill to your project
cp -r claude-skills/.claude/skills/pdf-master your-project/.claude/skills/
```

Or install globally:

```bash
cp -r claude-skills/.claude/skills/pdf-master ~/.claude/skills/
```

### Other AI Assistants

| Assistant | Skill Directory |
|-----------|-----------------|
| Claude Code | `.claude/skills/` |
| Cursor | `.cursor/skills/` |
| Windsurf | `.windsurf/skills/` |
| Continue | `.continue/skills/` |

## Skill Format

Each skill follows this structure:

```
skill-name/
└── SKILL.md    # Skill definition with instructions
```

The `SKILL.md` file contains:
- **Frontmatter** - Name and description for auto-activation
- **When to Apply** - Conditions for using the skill
- **Quick Reference** - Code patterns and best practices
- **Anti-Patterns** - Common mistakes to avoid

## Creating Skills

See the existing skills for examples of the format. Key sections:

```markdown
---
name: skill-name
description: "Keywords and actions that trigger this skill..."
---

# Skill Name

## When to Apply
### Must Use
### Recommended
### Skip

## Quick Reference
### Category 1 (CRITICAL)
### Category 2 (HIGH)

## Anti-Patterns
## Pre-Delivery Checklist
```

## License

MIT
