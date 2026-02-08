# codex-vs-fold-history

中文说明：[`README-zh.md`](./README-zh.md)

Patch the installed OpenAI Codex VS Code extension (`openai.chatgpt`) to:

- **Fold per-turn workflows** (thinking / tool calls / tool output / statuses / errors) into a single expandable `Workflow` row.
- **Scope chat history to the current workspace**: when you open a VS Code project, the Codex sidebar only shows threads whose `cwd` is inside the active workspace roots.

This project does **not** build upstream extension sources. It patches the installed **build artifacts in place**, creates `*.bak` backups (only once), and is fully reversible.

---

## Install (remote-first, no clone)

**Prereqs**

- VS Code installed
- OpenAI Codex extension installed (publisher: OpenAI, id: `openai.chatgpt`)
- Node.js available in `PATH`

### Option A (recommended): ask Codex to install it

Paste this into the **Codex chat**:

```text
Strictly follow https://github.com/abgyjaguo/codex-vs-fold-history/blob/main/docs/INSTALL_FOR_AI.md to install this feature. Do not improvise.
```

Then **restart VS Code**.

### Option B: run the remote installer script yourself

Windows (PowerShell):

```powershell
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/abgyjaguo/codex-vs-fold-history/main/docs/remote/codex-folding-install.mjs" -OutFile "$env:TEMP\codex-folding-install.mjs"
node "$env:TEMP\codex-folding-install.mjs"
```

macOS/Linux (bash/zsh):

```bash
curl -L "https://raw.githubusercontent.com/abgyjaguo/codex-vs-fold-history/main/docs/remote/codex-folding-install.mjs" -o "${TMPDIR:-/tmp}/codex-folding-install.mjs"
node "${TMPDIR:-/tmp}/codex-folding-install.mjs"
```

Then **restart VS Code**.

---

## Update on another computer

This feature is **machine-local** (it patches installed extension files on each device).

To update or enable it on another computer:

1) Install VS Code, `openai.chatgpt`, and Node.js on that computer.
2) Run the same install command/script from this repo.
3) Restart VS Code.
4) Keep `codex.workflow.threadScope` as `"workspace"` (or set `"all"` if you want all threads).

If `openai.chatgpt` updates later on that computer, run the installer again.

---

## Settings

Configure via **VS Code Settings**:

- `codex.workflow.collapseByDefault`
  - `"collapse"` (default): folded by default
  - `"expand"`: expanded by default
  - `"disable"`: disable folding (upstream behavior)
- `codex.workflow.threadScope`
  - `"workspace"` (default): only show threads whose `cwd` is inside active workspace roots
  - `"all"`: show all threads (upstream behavior)

---

## Troubleshooting

- **Thread list is empty after install**: set `codex.workflow.threadScope` to `"all"` to confirm your history exists; if it does, your `cwd` values likely fall outside the current workspace roots.
- **After the extension updates**: rerun the install script (updates replace the patched build artifacts).

---

## Uninstall

Paste this into the **Codex chat**:

```text
Strictly follow https://github.com/abgyjaguo/codex-vs-fold-history/blob/main/docs/UNINSTALL_FOR_AI.md to uninstall this feature. Do not improvise.
```

Then **restart VS Code**.

---

## What gets modified on your machine

The installer patches these installed extension artifacts (and creates `*.bak` backups):

- `out/extension.js`
- `webview/assets/index-*.js` (the active bundle referenced by `webview/index.html`)
- `webview/assets/zh-CN-*.js` (if present)

---

## Development / customization

- Patch logic: `tools/workflow-fold/patcher.mjs`
- Inspect local installed extension: `npm run inspect`
- Tests: `npm test`
- Apply patch to local installed extension: `npm run apply`
- Verify (dry-run, idempotent): `npm run verify`

When the upstream `openai.chatgpt` extension updates and changes its internal bundle structure, this repo may need new anchors / a new profile under `docs/patch-profiles/openai.chatgpt/`.

---

## License

MIT (see `LICENSE`).
