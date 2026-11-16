# omarchy-trip

记录在 [Omarchy](https://omarchy.org/) 系统上的日常折腾、客制化配置与清理纪要，方便回溯原理与在新环境快速复现。

## 目录索引

### 📦 软件包与工具链（`packages/`）
- [Mise 懒加载机制解析与未安装工具清理](packages/mise-cleanup.md)
  - 为什么 Omarchy 会内置大量 `mise`（`lazy = true`）命令及 shims
  - 彻底移除本地 `cursor-agent` 与 `copilot` 包及缓存
  - 通过 `~/.config/mise/config.toml` 的 `disable_tools` 清理所有未安装命令的 shim 链接

### ⌨️ 快捷键与终端（`keybindings/`）
- [Hyprland 快捷键覆盖（Cmd/Ctrl 调整）](keybindings/hyprland-overrides.md)
  - `Cmd/Super + Space` 与 `Ctrl + Space` 互换（输入法切换 ↔ Omarchy 菜单）
  - 将快捷键速查菜单从 `Cmd/Super + K` 移至 `Ctrl + K`，释放 `Cmd/Super + K`
- [Foot 终端配置（Cmd + K 清屏）](keybindings/terminal-foot.md)
  - 在 `foot.ini` 中配置 `Mod4+k` 发送 `Ctrl+L`（`\x0c`）清除终端上下文

### 🇨🇳 输入法（`input-method/`）
- [Fcitx5 中文输入法安装与切换为 WowType](input-method/fcitx5-chinese.md)
  - 从 `fcitx5-chinese-addons` 切换为 `WowType` 拼音引擎及卸载旧拼音包
  - 输入法配置、`omarchy-fcitx5.service` 托管与 `Cmd/Super + Space` 切换联动

### 🧩 Shell 与顶栏（`shell/`）
- [顶栏组件管理与移除 Agents 订阅面板（`omarchy.agents`）](shell/bar-widgets.md)
  - Omarchy Shell 顶栏布局配置与 `bar-widget` 插件启用/禁用机制
  - 通过 `omarchy plugin disable omarchy.agents` 从顶栏移除本地订阅用量面板
  - 查看插件状态、恢复组件及调整顶栏分区顺序的常用命令

## 核心涉及的配置文件

| 用途 | 配置文件路径 |
| :--- | :--- |
| 用户级 `mise` 工具与禁用列表 | `~/.config/mise/config.toml` |
| 系统级 Omarchy `mise` 默认工具（只读参考） | `/etc/mise/conf.d/omarchy-tools.toml` |
| Hyprland 个人快捷键覆盖 | `~/.config/hypr/bindings.lua` |
| Foot 终端配置 | `~/.config/foot/foot.ini` |
| Fcitx5 主配置与输入法列表 | `~/.config/fcitx5/config`、`~/.config/fcitx5/profile` |
| Omarchy Shell 与顶栏布局配置 | `~/.config/omarchy/shell.json` |
| 系统级 Omarchy Shell 默认模板（只读参考） | `/usr/share/omarchy/config/omarchy/shell.json` |
