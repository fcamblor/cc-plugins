# Claude Code Plugins Repository

A curated collection of Claude Code plugins containing reusable skills designed for selective import across projects.

## What are Plugins and Skills?

**Plugins** are collections of related **Skills** that extend Claude's capabilities with modular, context-aware expertise.

Key characteristics:
- **Model-invoked**: Claude autonomously decides when to use a skill based on context
- **Context-aware**: Automatically activated for relevant tasks
- **Organized**: Related skills are grouped within plugins for better discoverability

## Installation

See [INSTALLATION.md](./INSTALLATION.md) for detailed setup instructions (marketplace or manual install).

## Available Plugins

| Plugin | Description |
|--------|-------------|
| **typescript-standards** | TypeScript coding standards, validation patterns, and best practices using Zod and ts-pattern. |

## Structure

Each plugin contains:
- `plugin.json` - Plugin metadata
- `README.md` - Plugin documentation
- `skills/` - Skills provided by this plugin

## References

This repository is built on principles from:
- [Anthropic's Official Skills Repository](https://github.com/anthropics/skills)
- [Claude Code Plugins Documentation](https://code.claude.com/docs)

## License

Use these plugins freely across your projects.
