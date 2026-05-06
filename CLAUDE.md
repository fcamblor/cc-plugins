# CLAUDE.md

## Project overview

This repository is a marketplace of Claude Code plugins — each plugin packages one or more reusable skills that Claude can invoke autonomously across projects.

## Repository structure

```
.claude-plugin/marketplace.json   # Marketplace metadata and plugin registry
plugins/
  <plugin-name>/
    .claude-plugin/plugin.json    # Plugin metadata (name, version, description)
    README.md
    skills/
      <skill-name>/               # One skill per subdirectory
```

## Adding a plugin

1. Create `plugins/<plugin-name>/` with the structure above.
2. Register the plugin in `.claude-plugin/marketplace.json` under the `plugins` array.
3. Increment the marketplace `version` field in `marketplace.json`.

## Releasing

Releases are handled by a `mise` task. Run from any branch:

```bash
mise run release
```

The task interactively:
- Prompts for a new marketplace version (patch bump suggested from latest tag on `main`)
- Detects plugins modified since the last git tag and prompts for their new versions
- Merges your branch into `main`, updates `marketplace.json` and all `plugin.json` files, and updates `CHANGELOG.md` if present
- Creates a commit, a git tag `vX.Y.Z`, and pushes both to the remote

**Prerequisite:** [mise](https://mise.jdx.dev/) must be installed (`brew install mise`).

## Versioning

- `marketplace.json` version tracks the overall marketplace release (git tag).
- Each `plugin.json` version tracks that plugin independently; only plugins with changes since the last tag are bumped during a release.
- All versions follow [Semantic Versioning](https://semver.org/).
