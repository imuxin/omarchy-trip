# Mise 懒加载机制解析与未安装工具清理

## 1. 为什么 Omarchy 要内置这么多命令？

Omarchy 通过系统软件包 `omarchy-settings` 在 `/etc/mise/conf.d/` 下放置了三份配置：

- `/etc/mise/conf.d/omarchy-tools.toml`：预配置了大量开发与 Agent CLI 工具（如 `claude`、`codex`、`copilot`、`cursor-agent`、`opencode`、`pi`、`crush`、`devin`、`kiro-cli`、`qwen`、`uv` 等）。
- `/etc/mise/conf.d/omarchy.toml`：设置 `upgrade.auto_prune = false`（防止工具运行期间升级删除旧版本目录），并对 `cursor-agent` 做 `bin_path` 隔离，避免其自带 Node 覆盖系统/用户的 `node`。
- `/etc/mise/conf.d/omarchy-agent-accounts.toml`：为 `claude`、`codex`、`grok` 配置 `omarchy-agent-account-exec` wrapper，用于在执行时动态切换订阅账号。

### 设计机制：`lazy = true` + `auto_update = "4h"`
- **按需懒加载**：`omarchy-tools.toml` 中的绝大多数工具都带 `lazy = true`。初始状态不下载本体，只在 `~/.local/share/mise/shims/` 下生成指向 `/usr/bin/mise` 的软链接；首次在终端执行时才现场下载安装。
- **定期自更新**：多数 Agent CLI 配有 `auto_update = "4h"`，运行时最多每 4 小时检查一次更新。

---

## 2. 移除本地 `cursor-agent` 与 `copilot` 包及缓存

如果曾经触发过 `cursor-agent` 或 `copilot`，会在本地占用约 1.9 GB 空间（安装本体 + 运行时缓存 + CAS 下载缓存）。

### 卸载本地已安装版本

```bash
mise uninstall --all cursor-agent copilot
```

### 清理残留缓存与下载产物

```bash
rm -rf \
  ~/.cache/copilot \
  ~/.cache/cursor-compile-cache \
  ~/.cache/mise/copilot \
  ~/.cache/mise/cursor-agent \
  ~/.local/share/mise/downloads/copilot \
  ~/.local/share/mise/downloads/cursor-agent \
  ~/.local/state/mise/incomplete-installs/copilot \
  ~/.local/state/mise/incomplete-installs/cursor-agent
```

同时可按需核对 `~/.cache/mise/downloads-cas/urls/*.json` 与 `~/.cache/mise/downloads-cas/pins/sha256/*.json`，删除对应 `agent-cli-package.tar.gz` / `copilot-linux-x64.tar.gz` 的 `blake3` 缓存 blob，彻底释放下载缓存。

> 注：`~/.config/cursor/`（CLI 配置与本地状态）可根据需要决定是否保留。

---

## 3. 清理 `~/.local/share/mise/shims/` 中未实际安装的命令

**不要只手动删除 `~/.local/share/mise/shims/` 下的软链接**：因为 `/etc/mise/conf.d/omarchy-tools.toml` 依然声明了这些 `lazy = true` 工具，一旦运行 `mise reshim` / `mise up` / `mise install`，或者误执行命令、被工具扫描到 shim，都会重新生成链接甚至自动下载。

### 正确做法：在用户配置中使用 `disable_tools`

`/etc/mise/conf.d/omarchy-tools.toml` 属于系统包 `omarchy-settings`（更新时会被覆盖），应在用户级 `~/.config/mise/config.toml` 中通过 `[settings] -> disable_tools` 覆盖：

```toml
[tools]
bun = "latest"
go = "latest"

[settings]
disable_tools = [
  "amp",
  "antigravity-cli",
  "basecamp",
  "claude",
  "codex",
  "copilot",
  "cursor-agent",
  "ghui",
  "grok",
  "hey-cli",
  "http:kiro-cli",
  "hunk",
  "muse",
  "npm:@deepseek-ai/dsh",
  "npm:@kilocode/cli",
  "npm:@minimax-ai/code",
  "npm:@moonshot-ai/kimi-code",
  "npm:cf",
  "npm:command-code",
  "npm:freebuff",
  "oh-my-pi",
  "opencode",
  "ori",
  "playwright",
  "qwen",
  "uv",
]

[settings.upgrade]
```

### 重建 shims 并验证

```bash
mise reshim --force
mise ls
ls -la ~/.local/share/mise/shims/
```

清理后，`~/.local/share/mise/shims/` 仅保留本地实际已安装工具的命令：
`bun`, `bunx`, `crush`, `devin`, `gh`, `go`, `gofmt`, `node`, `npm`, `npx`, `pi`。

### 后续恢复某个工具

只需将该工具名从 `~/.config/mise/config.toml` 的 `disable_tools` 数组中移除，再运行 `mise reshim` 即可。
