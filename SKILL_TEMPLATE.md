---
name: skill-template
description: |
  Template for creating a new Claude Code skill.
  Use this as a starting point for new skills. Copy this file to your skill directory
  and customize the frontmatter and content for your specific use case.
allowed-tools: []
---

# Skill Template

This is a template for creating Claude Code skills. Use it as a reference when building new skills.

## File Structure

```
your-skill-name/
├── SKILL.md              # This file (required)
├── reference/            # Reference materials (optional)
│   └── *.md
├── templates/            # Code/text templates (optional)
│   └── *
└── scripts/              # Executable scripts (optional)
    └── *.sh
```

## Frontmatter Fields

Required fields in the YAML frontmatter:

```yaml
---
name: skill-name              # Required: lowercase, hyphens only, max 64 chars
description: |               # Required: what this skill does, max 1024 chars
  Clear description of what this skill does.
  Include specific triggers and use cases.
allowed-tools: [tool1, tool2] # Optional: restrict tool access
---
```

## Content Sections

Structure your skill documentation with these sections:

### Purpose

Brief explanation of what the skill does and its primary use case.

### When to Use This Skill

List specific scenarios and triggers that should activate this skill.
Be specific—this helps Claude understand when to use it.

### Key Capabilities

- Capability 1
- Capability 2
- Capability 3

### Examples

Provide practical, concrete examples of using this skill.

```example
Example code or workflow here
```

### Best Practices

Guidelines and best practices for applying this skill.

### Limitations

Document what this skill doesn't handle or edge cases to be aware of.

### References

Link to supporting files or external documentation:
- See `./reference/guidelines.md` for detailed guidelines
- See `./templates/` for code templates

## Tips for Effective Skills

1. **Specific Descriptions**: The description is how Claude discovers your skill
   - ✓ "Validates TypeScript code, checks for type errors, and ensures naming conventions"
   - ✗ "Helps with TypeScript stuff"

2. **Single Responsibility**: Each skill should focus on one capability
   - ✓ `typescript-validation`
   - ✗ `general-code-helpers`

3. **Practical Examples**: Show real-world usage scenarios

4. **Clear Boundaries**: Document what the skill does NOT handle

5. **Supporting Files**: Use subdirectories for organization
   - `reference/` for guidelines and standards
   - `templates/` for code or document templates
   - `scripts/` for executable scripts

## Naming Conventions

- Use kebab-case: `my-skill-name`
- Be descriptive: `typescript-validation` not `ts-check`
- Match the capability: skill name should clearly indicate purpose

## Integration with Projects

Once created, copy the skill to a project:

```bash
# Copy to project skills
cp -r your-skill-name /path/to/project/.claude/skills/

# Or to global skills
cp -r your-skill-name ~/.claude/skills/
```

Claude will automatically discover and use the skill based on its description.

---

**Ready to create a skill?**

1. Copy this template to `skills/your-skill-name/SKILL.md`
2. Customize the frontmatter
3. Write your skill's content
4. Add supporting files in subdirectories
5. Commit and share!
