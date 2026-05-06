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
| **oss-security-audit** | Security audit of third-party packages (npm, pip, homebrew, cargo…) — detects network calls, telemetry, eval, install hooks and recursively audits same-author dependencies. |

## Structure

Each plugin contains:
- `plugin.json` - Plugin metadata
- `README.md` - Plugin documentation
- `skills/` - Skills provided by this plugin

## Releasing

Releases are managed with [mise](https://mise.jdx.dev/). Each release bumps the marketplace version and optionally bumps individual plugin versions for plugins modified since the last tag.

**Prerequisites:** install mise (`brew install mise` or see [mise docs](https://mise.jdx.dev/getting-started.html)).

**Run a release from your feature branch:**

```bash
mise run release
```

The task will:
1. Prompt for a marketplace version (suggests a patch bump from the latest git tag on `main`)
2. Detect which plugins changed since the last tag and prompt for their new versions
3. Show a summary and ask for confirmation
4. Merge your branch into `main`, update `marketplace.json` and all `plugin.json` files, update `CHANGELOG.md` if present
5. Commit, create a git tag `vX.Y.Z`, and push both tag and `main` to the remote

## References

This repository is built on principles from:
- [Anthropic's Official Skills Repository](https://github.com/anthropics/skills)
- [Claude Code Plugins Documentation](https://code.claude.com/docs)

## License

Use these plugins freely across your projects.
