# oss-security-audit

Security audit plugin for third-party open-source packages before adoption.

## What it does

Before adding a dependency you've never used before, this plugin gives Claude
a structured checklist to audit its source code for supply-chain risks:

- Outbound network calls, telemetry, or data exfiltration
- Dynamic code execution (`eval`, shell commands)
- Obfuscated payloads (base64 decode-then-execute)
- Dangerous install-time hooks (`postinstall`, `build.rs`, `setup.py` overrides)
- Unexpected filesystem writes outside the project root
- Environment variable harvesting (credentials, tokens)
- Pre-compiled binary blobs committed to source
- Same-author dependency chain (recursive audit)
- Supply-chain hygiene (unpinned `@latest`, unsigned binaries, `curl|bash` installers)

## Supported package managers

| Manager | How source is obtained |
|---|---|
| npm / pnpm / yarn | `npm pack` or clone repo from `repository` field |
| pip | `pip download` + unzip `.whl`, or clone from PyPI source URL |
| homebrew | `brew cat <formula>` + clone formula `url` |
| cargo | crates.io source download or `git clone` |
| gem | `gem fetch` + `gem unpack` |

## Skills

### `audit-oss-package`

**Trigger:** user says "is this package safe", "audit this dependency",
"check this npm/pip/brew package before I use it", "can I trust this library",
or asks whether a package is trustworthy.

**Process (7 steps):**
1. Locate and fetch the package source
2. Read the manifest (lifecycle scripts, author, deps)
3. Enumerate source files (binary blobs, lock file)
4. Grep for suspicious patterns (network, exec, eval, telemetry, writes, env)
5. Assess each finding (severity + context)
6. Recursively audit same-author dependencies
7. Produce a structured report with verdict and recommendations

**Output:** a Markdown report with a one-line verdict, a findings table with
severity ratings, a same-author dependency table, and prioritised recommendations.

## Installation

```bash
# Copy plugin to local Claude plugins directory
cp -r plugins/oss-security-audit ~/.claude/plugins/

# Restart Claude Code to pick up the new skill
```

## Example usage

```
Audit this package and tell me if there is anything suspicious.
If any dependencies from the same author are used, fetch their
source and apply the same analysis.
```

```
Is the npm package `some-cli-tool` safe to add to our project?
```

```
Audit https://github.com/author/repo before we fork it
```

## Limitations

- Analyzes **source code**, not runtime behavior (dynamic config fetched post-install
  is out of scope).
- Compiled languages (Rust, Go, C) require inspecting the build pipeline in addition
  to source.
- Deeply nested transitive dependencies are not fully audited — only same-author
  direct dependencies are in scope by default.
- Does **not** replace `npm audit` / `pip-audit` for known CVE scanning.

## Files

```
oss-security-audit/
├── .claude-plugin/
│   └── plugin.json
├── README.md
└── skills/
    └── audit-oss-package/
        ├── SKILL.md                          # Skill definition + 7-step process
        └── reference/
            └── suspicious-patterns.md        # Full grep command reference per category
```
