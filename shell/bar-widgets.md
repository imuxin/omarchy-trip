# 顶栏组件管理与移除 Agents 订阅面板（`omarchy.agents`）

## 1. 顶栏机制与插件定位

Omarchy 顶栏由长期运行的 `omarchy-shell` 进程及其内置栏位插件 `omarchy.bar` 渲染：

- **默认配置模板（只读参考）**：`/usr/share/omarchy/config/omarchy/shell.json`
- **用户实际生效配置**：`~/.config/omarchy/shell.json`（一旦用户通过交互手势、`omarchy bar` / `omarchy plugin` 命令或手动编辑生成该文件，即以用户文件为唯一准绳，不再与系统默认配置做深合并）
- **内置插件目录**：`/usr/share/omarchy/shell/plugins/`
- **第三方用户插件目录**：`~/.config/omarchy/plugins/<plugin-id>/`

### 顶栏中的 `omarchy.agents` 是什么？

顶栏右侧默认出现的 AI 订阅/额度图标并非独立的系统托盘进程，而是内置的 `bar-widget` 插件 **`omarchy.agents`**：

- **插件清单**：`/usr/share/omarchy/shell/plugins/agents/manifest.json`（`kinds: ["bar-widget"]`）
- **数据来源**：面板展示 `omarchy-agent-usage-update` 写入 `~/.local/state/omarchy/agents/usage/` 的各订阅额度与用量记录
- **区别于 `usemagpie.magpie`**：如果顶栏同时存在 `usemagpie.magpie`，它是安装在 `~/.config/omarchy/plugins/usemagpie.magpie/` 的第三方 `bar-widget`（用于快速唤起 Magpie 模型/窗口面板），而非内置的订阅用量追踪器

对于 `bar-widget` 类型的插件，`omarchy plugin enable / disable` 控制的是它是否出现在顶栏布局中，而不是从磁盘删除插件代码。

---

## 2. 从顶栏移除 `omarchy.agents`

### 方式一：使用官方 CLI（推荐）

```bash
omarchy plugin disable omarchy.agents
```

该命令（对应 `/usr/share/omarchy/bin/omarchy-plugin-disable`）会通过 IPC 调用 `omarchy-shell shell setPluginEnabled omarchy.agents false`，立即将图标从运行中的顶栏移除并同步持久化到 `~/.config/omarchy/shell.json`。

### 方式二：手动编辑 `~/.config/omarchy/shell.json`

从 `bar.layout.right` 数组中删除以下条目：

```json
{
  "id": "omarchy.agents"
}
```

> 注：若目标其实是移除第三方 `usemagpie.magpie` 图标，可执行 `omarchy plugin disable usemagpie.magpie`（仅将其移出顶栏，保留 `~/.config/omarchy/plugins/usemagpie.magpie/` 目录）。

---

## 3. 恢复、查看与调整顶栏组件

### 查看所有插件与当前启用状态

```bash
omarchy plugin list
```

### 重新放回顶栏

```bash
# 恢复到默认/原位置
omarchy plugin enable omarchy.agents

# 或显式指定目标分区与相对位置
omarchy plugin enable omarchy.agents --section right --after omarchy.tailscale
```

### 调整已有组件顺序或分区

```bash
# 查看 bar 命令帮助
omarchy bar --help

# 移动已有组件到指定分区或位置（分区可选：left、centerBegin、center、centerEnd、tray、right）
omarchy bar move <id> --section right --before omarchy.bluetooth
```
