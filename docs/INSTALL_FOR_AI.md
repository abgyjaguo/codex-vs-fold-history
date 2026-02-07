# Install Guide (For AI Operators) — Remote-First (No Clone)

> Goal: install **Workflow folding + workspace-scoped history** into the installed Codex VS Code extension (`openai.chatgpt`) on Windows/macOS/Linux by running a remote, deterministic patch script with backups.

**What this does (context for AI):**

- Folds all pre-final process items (thinking / tool calls / tool output / status / error) into a per-turn `Workflow` expandable row.
- Filters the thread list to the active VS Code workspace roots (by `cwd`) so the sidebar only shows chats for the project you opened.

## Canonical URLs (source of truth)

- This file:
  - `https://github.com/abgyjaguo/codex-vs-fold-history/blob/main/docs/INSTALL_FOR_AI.md`
- Remote install script (single file):
  - `https://raw.githubusercontent.com/abgyjaguo/codex-vs-fold-history/main/docs/remote/codex-folding-install.mjs`
- Remote uninstall script (single file):
  - `https://raw.githubusercontent.com/abgyjaguo/codex-vs-fold-history/main/docs/remote/codex-folding-uninstall.mjs`
- Manual (for user to save):
  - `https://raw.githubusercontent.com/abgyjaguo/codex-vs-fold-history/main/docs/AI_OPERATOR_MANUAL.md`

## Preconditions

- VS Code installed
- Codex extension installed (Publisher: OpenAI, Extension id: `openai.chatgpt`)
- Node.js available in `PATH`
- A shell to run the commands:
  - Windows: PowerShell
  - macOS/Linux: bash/zsh (any POSIX shell)

## Strict Rules (anti-improvisation)

- Do **NOT** clone the repo.
- Do **NOT** re-implement patch logic.
- Only download and run the provided remote script(s).
- If the script reports incompatible anchors, **STOP** and explain what is different.

## Steps (what to do)

### 1) Download the remote install script

- Windows (PowerShell):
  - `Invoke-WebRequest -Uri "https://raw.githubusercontent.com/abgyjaguo/codex-vs-fold-history/main/docs/remote/codex-folding-install.mjs" -OutFile "$env:TEMP\\codex-folding-install.mjs"`
  - If `raw.githubusercontent.com` is blocked:
    - `Invoke-WebRequest -Uri "https://github.com/abgyjaguo/codex-vs-fold-history/raw/main/docs/remote/codex-folding-install.mjs" -OutFile "$env:TEMP\\codex-folding-install.mjs"`
- macOS/Linux (bash/zsh):
  - `curl -L "https://raw.githubusercontent.com/abgyjaguo/codex-vs-fold-history/main/docs/remote/codex-folding-install.mjs" -o "${TMPDIR:-/tmp}/codex-folding-install.mjs"`
  - If `raw.githubusercontent.com` is blocked:
    - `curl -L "https://github.com/abgyjaguo/codex-vs-fold-history/raw/main/docs/remote/codex-folding-install.mjs" -o "${TMPDIR:-/tmp}/codex-folding-install.mjs"`

### 2) Run install script (patches extension + saves manual)

- Windows (PowerShell): `node "$env:TEMP\\codex-folding-install.mjs"`
- macOS/Linux (bash/zsh): `node "${TMPDIR:-/tmp}/codex-folding-install.mjs"`

Expected behavior:

- Finds the installed extension folder under one of:
  - `~/.vscode/extensions/openai.chatgpt-*` (VS Code Stable)
  - `~/.vscode-insiders/extensions/openai.chatgpt-*` (VS Code Insiders)
  - `~/.vscode-oss/extensions/openai.chatgpt-*` (some OSS builds / VSCodium)
- Checks key anchors in the target files (to decide compatibility)
- Patches 2–3 files and creates `*.bak` backups (only once)
  - `zh-CN-*.js` may be absent on some builds; the installer will warn and continue
- Downloads the AI operator manual and saves it to a user-friendly location (prefer `Downloads`)
- Prints absolute paths of modified files and backups

### 3) Restart VS Code

- Required for the extension webview to pick up patched artifacts.

## Optional: settings (preferred over `~/.codex/config.toml`)

This project does **not** use `~/.codex/config.toml` anymore. Behavior is controlled via **VS Code Settings**:

- Key: `codex.workflow.collapseByDefault`
- Values:
  - `"collapse"`: show Workflow, default collapsed (recommended)
  - `"expand"`: show Workflow, default expanded
  - `"disable"`: disable feature (upstream behavior)
- Default: if unset (or the injected `<meta>` is missing), the patched webview behaves like `"collapse"`.

- Key: `codex.workflow.threadScope`
- Values:
  - `"workspace"`: only show chats whose `cwd` is inside the active VS Code workspace roots (recommended)
  - `"all"`: show all chats (upstream behavior)
- Default: `"workspace"`

## What gets modified on the user machine

- Installed extension build artifacts (plus `*.bak` backups):
  - `out/extension.js`
  - `webview/assets/index-*.js` (the active bundle referenced by `webview/index.html`)
  - `webview/assets/zh-CN-*.js` (if present)
- VS Code settings (optional; installer does not modify it)
