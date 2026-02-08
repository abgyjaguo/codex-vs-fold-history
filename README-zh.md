# codex-vs-fold-history

英文版：[`README.md`](./README.md)

给已安装的 OpenAI Codex VS Code 扩展（`openai.chatgpt`）打补丁，实现：

- **按轮折叠工作流**：把每轮对话中的「思考 / 工具调用 / 工具输出 / 状态 / 错误」收纳为一条可展开的 `Workflow` 行。
- **只显示当前项目的聊天记录**：当你打开一个 VS Code 项目时，Codex 侧边栏只展示 `cwd` 位于当前工作区根目录（workspace roots）内的线程。

本项目**不需要**拿到上游扩展源码并构建；而是对你本机已安装的扩展**产物文件就地打补丁**，自动创建 `*.bak` 备份（只备份一次），可随时卸载回滚。

---

## 安装（远程脚本优先，不需要 clone）

**前置条件**

- 已安装 VS Code
- 已安装 OpenAI Codex 扩展（发布者 OpenAI，扩展 id：`openai.chatgpt`）
- `PATH` 中可用的 Node.js

### 方式 A（推荐）：让 Codex 代你安装

把下面这句话发给 **Codex 聊天**：

```text
Strictly follow https://github.com/abgyjaguo/codex-vs-fold-history/blob/main/docs/INSTALL_FOR_AI.md to install this feature. Do not improvise.
```

然后 **重启 VS Code** 生效。

### 方式 B：手动运行远程安装脚本

Windows（PowerShell）：

```powershell
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/abgyjaguo/codex-vs-fold-history/main/docs/remote/codex-folding-install.mjs" -OutFile "$env:TEMP\codex-folding-install.mjs"
node "$env:TEMP\codex-folding-install.mjs"
```

macOS/Linux（bash/zsh）：

```bash
curl -L "https://raw.githubusercontent.com/abgyjaguo/codex-vs-fold-history/main/docs/remote/codex-folding-install.mjs" -o "${TMPDIR:-/tmp}/codex-folding-install.mjs"
node "${TMPDIR:-/tmp}/codex-folding-install.mjs"
```

然后 **重启 VS Code** 生效。

---

## 配置（VS Code 设置）

在 VS Code 设置里配置：

- `codex.workflow.collapseByDefault`
  - `"collapse"`（默认）：默认折叠
  - `"expand"`：默认展开
  - `"disable"`：禁用折叠（恢复上游行为）
- `codex.workflow.threadScope`
  - `"workspace"`（默认）：只显示 `cwd` 在当前工作区根目录内的线程
  - `"all"`：显示全部线程（恢复上游行为）

---

## 常见问题

- **安装后列表为空**：先把 `codex.workflow.threadScope` 设为 `"all"` 验证历史是否存在；如果能看到历史，说明你的线程 `cwd` 不在当前工作区根目录内。
- **扩展升级后失效**：上游扩展更新会覆盖已打补丁的产物文件，重新运行安装脚本即可。

---

## 卸载

把下面这句话发给 **Codex 聊天**：

```text
Strictly follow https://github.com/abgyjaguo/codex-vs-fold-history/blob/main/docs/UNINSTALL_FOR_AI.md to uninstall this feature. Do not improvise.
```

然后 **重启 VS Code** 生效。

---

## 会修改你机器上的哪些文件

安装脚本会对已安装扩展的以下产物文件打补丁（并创建 `*.bak` 备份）：

- `out/extension.js`
- `webview/assets/index-*.js`（`webview/index.html` 引用的入口 bundle）
- `webview/assets/zh-CN-*.js`（如存在）

---

## 二次开发 / 适配更新

- 补丁逻辑入口：`tools/workflow-fold/patcher.mjs`
- 检查本机扩展版本与锚点：`npm run inspect`
- 运行测试：`npm test`
- 对本机已安装扩展应用补丁：`npm run apply`
- 验证（不落盘，幂等检查）：`npm run verify`

当上游 `openai.chatgpt` 扩展升级、内部 bundle 结构变动时，可能需要更新锚点并新增 profile：`docs/patch-profiles/openai.chatgpt/`。

---

## 许可证

MIT（见 `LICENSE`）。
