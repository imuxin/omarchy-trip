# Hyprland 快捷键覆盖（Cmd/Ctrl 调整）

配置文件：`~/.config/hypr/bindings.lua`

> 说明：在 Omarchy 中，`SUPER` 对应 Mac 键盘布局下的 `Cmd` 键。个人快捷键覆盖统一放在 `~/.config/hypr/bindings.lua`，先 `hl.unbind(...)` 解除默认绑定，再 `o.bind(...)` 重新绑定。

## 1. 互换 `Cmd/Super + Space` 与 `Ctrl + Space`

### Omarchy 默认行为
- `SUPER + SPACE`：打开 Omarchy 菜单 / 启动器。
- `CTRL + SPACE`：用于终端复用器前缀或直通输入法（参见 `/usr/share/omarchy/default/hypr/bindings/input-method.lua`）。

### 自定义覆盖
为了贴合 macOS 使用习惯（`Cmd + Space` 切换输入法，`Ctrl + Space` 呼出 Omarchy 菜单）：

```lua
-- Swap Cmd/Super+Space (Omarchy menu) and Ctrl+Space (input method).
hl.unbind("SUPER + SPACE")
hl.unbind("CTRL + SPACE")
o.bind("SUPER + SPACE", "Toggle input method", "fcitx5-remote -t")
o.bind("CTRL + SPACE", "Omarchy menu", "omarchy-menu toggle")
```

## 2. 释放 `Cmd/Super + K`（将快捷键速查移至 `Ctrl + K`）

### Omarchy 默认行为
- `/usr/share/omarchy/default/hypr/bindings/utilities.lua` 默认将 `SUPER + K` 绑定到 `omarchy-menu-keybindings`（快捷键速查面板）。

### 自定义覆盖
为让终端应用（如 Foot）能够接收到 `Cmd + K` 来清除终端上下文/屏幕，将 Hyprland 的快捷键速查改为 `Ctrl + K`：

```lua
-- Move the keybindings menu from Cmd/Super+K to Ctrl+K.
hl.unbind("SUPER + K")
hl.unbind("CTRL + K")
o.bind("CTRL + K", "Keybindings", "omarchy-menu-keybindings")
```

## 生效与排查

- 查看当前生效的快捷键列表：

  ```bash
  omarchy menu keybindings --print
  ```

- 终端侧对 `Cmd + K` 的清屏映射见：[Foot 终端配置](terminal-foot.md)。
