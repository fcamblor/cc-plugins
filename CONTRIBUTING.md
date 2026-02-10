# Contributing: Creating a New Plugin

This guide explains how to create and contribute a new plugin to this repository.

## Plugin Structure

Each plugin must follow this structure:

```
plugins/your-plugin-name/
├── .claude-plugin/
│   └── plugin.json              # Required: plugin metadata
├── README.md                    # Required: plugin documentation
└── skills/
    └── your-skill-name/
        ├── SKILL.md             # Required: skill definition
        ├── reference/           # Optional: detailed guides
        └── templates/           # Optional: code templates
```

## Step 1: Create Plugin Metadata

Create `.claude-plugin/plugin.json`:

```json
{
  "name": "your-plugin-name",
  "version": "0.0.1",
  "description": "Clear description of what this plugin provides",
  "author": {
    "name": "Your Name"
  },
  "keywords": ["tag1", "tag2", "tag3"],
  "category": "development"
}
```

**Categories:** `development`, `testing`, `debugging`, `collaboration`, `utilities`, `meta`

## Step 2: Create Plugin README

Create `README.md` at plugin root with:
- Brief description of the plugin
- What problems it solves
- Key features
- Installation instructions
- How to use the skills
- File structure
- Contributing/feedback section

See `plugins/typescript-standards/README.md` for an example.

## Step 3: Create Skills

Each skill goes in `skills/skill-name/`:

### SKILL.md Format

```yaml
---
name: skill-name
description: |
  Clear description of what this skill does and when Claude should use it.
  Include specific triggers and use cases (max 1024 characters).
allowed-tools: [tool1, tool2]  # Optional: restrict tool access
---

# Skill Title

## Purpose

What this skill does and its primary use case.

## When to Use

Specific scenarios and triggers that should activate this skill.

## Key Capabilities

- Capability 1
- Capability 2

## Examples

Practical usage examples.

## Limitations

What this skill doesn't handle.
```

### Supporting Files (Optional)

- **reference/** - Detailed documentation, guidelines, best practices
- **templates/** - Code templates or examples
- **scripts/** - Helper scripts (if applicable)

## Naming Conventions

- **Plugin name**: kebab-case, descriptive (e.g., `typescript-standards`)
- **Skill name**: kebab-case (e.g., `enforce-ts-standards`)
- **Files**: kebab-case (e.g., `typescript-rules.md`)
- **Variables/Functions**: camelCase
- **Types/Classes**: PascalCase

## Quality Guidelines

### Skill Description

The description is critical—Claude uses it to decide when to activate the skill:

```yaml
# ✅ Good: Clear triggers and scope
description: |
  Enforces type-safe TypeScript patterns. Use when writing or reviewing
  TypeScript for type safety, validation patterns, or preventing runtime errors.

# ❌ Poor: Too vague
description: |
  Helps with TypeScript
```

### Documentation

- Keep examples practical and actionable
- Document limitations explicitly
- Include real use cases
- Explain when NOT to use the skill

### Single Responsibility

Each skill should handle one primary capability:

```
✅ typescript-standards → Type safety patterns
✅ zod-validation → Runtime validation patterns
❌ typescript-everything → Too broad
```

## Adding to Marketplace

Once your plugin is ready, add it to `.claude-plugin/marketplace.json`:

```json
{
  "name": "fcamblor-cc-plugins",
  "owner": {
    "name": "fcamblor"
  },
  "metadata": {
    "description": "A curated collection of reusable Claude Code plugins",
    "version": "0.0.1"
  },
  "plugins": [
    {
      "name": "your-plugin-name",
      "source": "./plugins/your-plugin-name",
      "description": "What this plugin does",
      "author": {
        "name": "Your Name"
      },
      "keywords": ["tag1", "tag2"],
      "category": "development",
      "license": "MIT"
    }
  ]
}
```

## Testing Your Plugin

1. **Copy to local plugins directory:**
   ```bash
   cp -r your-plugin-name ~/.claude/plugins/
   ```

2. **Restart Claude Code** to refresh plugin discovery

3. **Test skill activation:**
   - Explicitly invoke skills in Claude Code
   - Verify implicit activation in relevant contexts
   - Refine descriptions if activation doesn't work as expected

## Submitting Your Plugin

1. Fork the repository
2. Create a feature branch: `git checkout -b add/your-plugin-name`
3. Add your plugin under `plugins/`
4. Update `.claude-plugin/marketplace.json` to include your plugin
5. Commit and push
6. Open a pull request with:
   - Brief description of the plugin
   - What problem it solves
   - Example usage

## Best Practices

### For Plugins

- **Organize by domain**: Group related skills within one plugin
- **Version carefully**: Follow semantic versioning
- **Document thoroughly**: Clear README helps discoverability
- **Test extensively**: Ensure skills activate reliably

### For Skills

- **Keep descriptions precise**: This determines discoverability
- **Avoid overlap**: Don't duplicate other skills' functionality
- **Include practical examples**: Show real-world usage
- **Document edge cases**: Explain limitations clearly

### For Documentation

- **Be specific**: Rather than "helps with X", say "does Y when Z"
- **Show, don't tell**: Include code examples
- **Link references**: Point to external docs when helpful
- **Keep it current**: Update docs when the skill changes

## Questions?

Review existing plugins:
- `plugins/typescript-standards/` - Complete example with reference materials

Refer to:
- [Claude Code Plugins Documentation](https://code.claude.com/docs)
- [Anthropic Skills Repository](https://github.com/anthropics/skills)

## License

Plugins contributed to this repository are shared under MIT license.
