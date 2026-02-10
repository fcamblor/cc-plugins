# Installation Guide

## Quick Start

### Install via Marketplace (Recommended)

The easiest way to install plugins is through the Claude Code plugin marketplace:

```bash
# Add the marketplace
claude plugin marketplace add fcamblor/cc-plugins

# Install a plugin
claude plugin install typescript-standards@fcamblor-cc-plugins

# View all available plugins
claude plugin marketplace list fcamblor-cc-plugins
```

That's it! The plugin and all its skills are now available in Claude Code.

## Manual Installation (Alternative)

If you prefer to install without the marketplace:

```bash
# Copy the plugin to your personal plugins directory
cp -r plugins/typescript-standards ~/.claude/plugins/
```

Or for project-specific installation:

```bash
# Copy to your project's plugins directory
cp -r plugins/typescript-standards ./.claude/plugins/
```

## Verifying Installation

To verify a plugin is installed correctly:

```bash
# Check global plugins
ls ~/.claude/plugins/

# Check project plugins
ls ./.claude/plugins/
```

## Available Plugins

| Plugin | Installation Command |
|--------|----------------------|
| **typescript-standards** | `claude plugin install typescript-standards@fcamblor-cc-plugins` |

## Using Installed Skills

Once a plugin is installed, its skills are automatically available:

1. **Explicit request**: Ask Claude to use a specific skill
   ```
   Use the enforce-ts-standards skill to check this code
   ```

2. **Implicit activation**: Claude detects relevant context
   ```
   Review this TypeScript code for type safety issues
   ```

## Troubleshooting

### Plugins not appearing

- Restart Claude Code to refresh the plugin discovery
- Verify the plugin directory exists with the correct structure
- Check that `plugin.json` is valid JSON

### Skills not activating

- Review the skill's `description` field—Claude uses this to determine when to activate it
- Make sure the plugin/skill is properly installed in the correct directory

## Next Steps

See individual plugin READMEs for detailed usage instructions:
- [typescript-standards README](./plugins/typescript-standards/README.md)
