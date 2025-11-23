# Installation Guide

## Quick Start

### Add a Skill to Your Current Project

```bash
# Navigate to your project root
cd /path/to/your/project

# Copy the skill from this repository
cp -r /path/to/cc-skills/skills/skill-name ./.claude/skills/

# Verify it was added
ls -la ./.claude/skills/skill-name/
```

### Add a Skill Globally (All Projects)

```bash
# Copy the skill to your personal skills directory
cp -r /path/to/cc-skills/skills/skill-name ~/.claude/skills/

# Verify it was added
ls -la ~/.claude/skills/skill-name/
```

## Directory Structure After Installation

### Project-Level Installation

```
your-project/
├── .claude/
│   ├── skills/
│   │   └── skill-name/        ← Copied skill
│   │       └── SKILL.md
│   └── CLAUDE.md
├── src/
└── package.json
```

### Global Installation

```
~/.claude/
├── skills/
│   └── skill-name/            ← Copied skill
│       └── SKILL.md
└── settings.json
```

## Organizing Skills by Domain

As your skill collection grows, organizing them by domain (category) improves discoverability and maintainability. This approach is inspired by production-grade libraries like [Superpowers](https://github.com/obra/superpowers).

### Recommended Structure

Group related skills by domain:

```
skills/
├── testing/
│   ├── test-driven-development/
│   │   └── SKILL.md
│   └── unit-testing/
│       └── SKILL.md
├── debugging/
│   ├── root-cause-analysis/
│   │   └── SKILL.md
│   └── verification/
│       └── SKILL.md
├── collaboration/
│   ├── code-review/
│   │   └── SKILL.md
│   └── design-brainstorm/
│       └── SKILL.md
├── development/
│   ├── typescript-validation/
│   │   └── SKILL.md
│   └── security-review/
│       └── SKILL.md
├── meta/
│   ├── skill-creator/
│   │   └── SKILL.md
│   └── template-skill/
│       └── SKILL.md
└── utils/
    ├── documentation/
    │   └── SKILL.md
    └── automation/
        └── SKILL.md
```

### Suggested Domains

- **testing/** - Test writing, TDD, validation strategies
- **debugging/** - Root cause analysis, troubleshooting, verification
- **collaboration/** - Code review, design, planning, team workflows
- **development/** - Language-specific skills, architecture, patterns
- **meta/** - Skill creation, tooling, repository management
- **utils/** - General utilities, documentation, automation

### Copying Skills by Domain

```bash
# Copy all testing skills to your project
cp -r /path/to/cc-skills/skills/testing/* ./.claude/skills/

# Copy specific domain skills globally
cp -r /path/to/cc-skills/skills/debugging/* ~/.claude/skills/

# Mix and match
cp -r /path/to/cc-skills/skills/development/typescript-validation ~/.claude/skills/
cp -r /path/to/cc-skills/skills/collaboration/code-review ~/.claude/skills/
```

## Using Skills in Claude Code

Once installed, skills are automatically discovered by Claude. To trigger a skill:

1. **Explicit Request**: Ask Claude directly to use the skill
   ```
   "Use the security-review skill to check this code"
   ```

2. **Implicit Activation**: Claude detects relevant context
   ```
   "Review this TypeScript code for issues"
   # → typescript-standards skill activates if installed
   ```

## Bulk Installation

### Add All Skills at Once

```bash
# Copy all skills from the repository to your project
cp -r /path/to/cc-skills/skills/* ./.claude/skills/

# Or to your global skills directory
cp -r /path/to/cc-skills/skills/* ~/.claude/skills/
```

### Set Up as Git Submodule (Advanced)

```bash
cd /path/to/your/project
git submodule add <repository-url> cc-skills

# Then copy skills as needed
cp -r cc-skills/skills/skill-name ./.claude/skills/
```

## Available Skills

Currently available skills in this repository:

| Skill | Description | Location |
|-------|-------------|----------|
| **typescript-standards** | TypeScript coding standards, validation patterns, and best practices. Covers type safety, Zod validation, ts-pattern matching, naming conventions, and pragmatic approaches for legacy code. | `skills/typescript-standards/` |

## Creating Your Own Skill

To add a new skill to this repository:

### 1. Create the Skill Directory

```bash
mkdir -p cc-skills/skills/your-skill-name/reference
mkdir -p cc-skills/skills/your-skill-name/templates
mkdir -p cc-skills/skills/your-skill-name/scripts
```

### 2. Create SKILL.md

```bash
cat > cc-skills/skills/your-skill-name/SKILL.md << 'EOF'
---
name: your-skill-name
description: |
  Clear, specific description of what this skill does.
  Include when Claude should use this skill (max 1024 chars).
allowed-tools: [tool1, tool2]
---

# Skill Title

## Purpose

Describe what this skill does and its primary use case.

## When to Use

List specific scenarios and triggers that should activate this skill.

## Key Capabilities

- Capability 1
- Capability 2
- Capability 3

## Examples

Show practical usage examples.

## Limitations

Document what this skill doesn't handle.
EOF
```

### 3. Add Supporting Files (Optional)

```
your-skill-name/
├── SKILL.md
├── reference/
│   ├── guidelines.md
│   └── best-practices.md
├── templates/
│   └── example-template.ext
└── scripts/
    └── helper-script.sh
```

### 4. Commit and Push

```bash
git add cc-skills/skills/your-skill-name/
git commit -m "Add your-skill-name skill"
git push
```

## Troubleshooting

### Skill Not Being Used by Claude

1. **Check discovery**: Claude uses the `description` field to activate skills
   - Verify the description clearly states when the skill applies
   - Make it specific and action-oriented

2. **Verify installation**: Confirm the skill is in the correct location
   ```bash
   # Project-level
   ls -la ./.claude/skills/skill-name/SKILL.md

   # Global
   ls -la ~/.claude/skills/skill-name/SKILL.md
   ```

3. **Reload Claude Code**: Restart Claude Code to refresh skill discovery

### Path Issues When Copying

Use absolute paths to avoid confusion:

```bash
# Good
cp -r ~/Projects/cc-skills/skills/my-skill ~/.claude/skills/

# Also good
cp -r /Users/username/Projects/cc-skills/skills/my-skill ~/.claude/skills/

# Avoid (relative paths)
cp -r ../cc-skills/skills/my-skill ~/.claude/skills/
```

## Best Practices

- **Keep it simple**: Start with focused, single-responsibility skills
- **Clear descriptions**: The description is how Claude discovers your skill
- **Version control**: Commit skills to your project's git repository
- **Document well**: Include examples and limitations in SKILL.md
- **Test iteratively**: Try using the skill and refine based on results

## Next Steps

Once you've installed a skill:

1. Try using it naturally in Claude Code
2. Observe when Claude applies it to your tasks
3. Refine the skill's description if needed
4. Add supporting files as your skill grows
5. Share improvements back to the repository

For questions about Claude Code skills, see the [README.md](./README.md).

## References

This guide incorporates best practices from:

- [Anthropic's Official Skills Repository](https://github.com/anthropics/skills) - Production-grade skill implementations and examples
- [Superpowers Repository](https://github.com/obra/superpowers) - Battle-tested skills library with organized domain structure
- [Claude Code Skills Documentation](https://code.claude.com/docs/en/skills.md) - Official Claude Code skill documentation
