# Claude Code Skills Repository

A reusable collection of Claude Code skills designed for selective import across projects.

## What are Skills?

Skills extend Claude's capabilities with modular, context-aware expertise. Unlike slash commands that require explicit invocation, skills are automatically used by Claude when relevant to your current task.

Key characteristics:
- **Model-invoked**: Claude autonomously decides when to use a skill based on context
- **Context-aware**: Automatically activated for relevant tasks
- **Structured**: Support multi-file organization (instructions, templates, scripts, reference materials)
- **Discoverable**: Claude reads the skill description to determine when it applies

## Repository Structure

```
cc-skills/
├── README.md              # This file
├── INSTALLATION.md        # Setup instructions
├── .gitignore
└── skills/
    ├── skill-name-1/      # First skill
    │   ├── SKILL.md
    │   ├── reference/
    │   ├── templates/
    │   └── scripts/
    ├── skill-name-2/      # Second skill
    │   └── SKILL.md
    └── ...
```

## Skill Anatomy

Each skill is a self-contained directory with this minimal structure:

```
skill-name/
├── SKILL.md              # Required: skill metadata + instructions
├── reference/            # Optional: documentation & guidelines
├── templates/            # Optional: code templates
└── scripts/              # Optional: executable scripts
```

### SKILL.md Format

Every skill uses a YAML frontmatter to describe itself:

```yaml
---
name: skill-name
description: |
  Clear description of what this skill does and when Claude should use it.
  Include specific triggers and use cases (max 1024 characters).
allowed-tools: [tool1, tool2]  # Optional: restrict tool access
---

# Skill Content

Regular Markdown content with instructions, guidelines, examples, etc.
```

## How to Use Skills from This Repository

### Option 1: Copy a Single Skill to Your Project

```bash
# Add the skill to your current project's .claude/skills/
cp -r cc-skills/skills/skill-name ./.claude/skills/

# Now the skill is available only in this project
```

### Option 2: Copy a Skill to Your Personal Skills

```bash
# Add the skill to your personal global skills
cp -r cc-skills/skills/skill-name ~/.claude/skills/

# Now the skill is available across all projects
```

### Option 3: Clone the Entire Repository

```bash
# Clone for reference or bulk skill access
git clone <repository-url> cc-skills

# Then copy individual skills as needed
cp -r cc-skills/skills/* ~/.claude/skills/
```

## Available Skills

See [INSTALLATION.md](./INSTALLATION.md) for detailed setup instructions and available skills.

## Creating Your First Skill

To add a new skill to this repository:

1. Create a directory under `skills/`
2. Add a `SKILL.md` file with YAML frontmatter
3. Add supporting files in subdirectories as needed
4. Commit and push to share with your projects

See the [INSTALLATION.md](./INSTALLATION.md) guide for a step-by-step example.

## Discovery & Best Practices

### Skill Description is Key

The `description` field in SKILL.md is how Claude discovers when to use your skill. Make it specific:

```yaml
# Good: Clear triggers and capabilities
description: |
  Extracts text and tables from PDFs, fills form fields, and merges documents.
  Use when working with PDF files, form processing, or document assembly tasks.

# Poor: Too vague
description: |
  Helps with documents
```

### Naming Conventions

- Use kebab-case: `my-skill-name`
- Be descriptive: `typescript-validation` not `ts-check`
- Match the capability: `security-review` for security tasks

### Organization Tips

- Keep each skill focused on a single responsibility
- Use supporting files (reference/, templates/, scripts/) for complex skills
- Include practical examples in SKILL.md
- Document limitations explicitly

## Integration with Projects

Once copied to a project's `.claude/skills/` directory:

```
your-project/
├── .claude/
│   ├── skills/
│   │   ├── skill-name-1/
│   │   │   └── SKILL.md
│   │   └── skill-name-2/
│   │       └── SKILL.md
│   └── CLAUDE.md
├── src/
└── package.json
```

Claude will automatically discover and use these skills when relevant to your task.

## License

Use these skills freely across your projects.
