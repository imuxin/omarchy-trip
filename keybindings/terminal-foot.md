# Foot 终端配置（`Cmd + K` 清除终端上下文）

默认终端顺序（`~/.config/xdg-terminals.list`）首选 `foot.desktop`，配置文件位于 `~/.config/foot/foot.ini`。

## `Cmd/Super + K` 清除终端屏幕/上下文

在释放了 Hyprland 的 `SUPER + K` 全局占用后（见 [Hyprland 快捷键覆盖](hyprland-overrides.md)），在 `~/.config/foot/foot.ini` 的 `[text-bindings]` 段中将 `Mod4+k`（即 `Super/Cmd + K`）映射为发送 `\x0c`（即 `Ctrl + L`，clear screen）：

```ini
[text-bindings]
# Cmd/Super+K sends Ctrl+L to clear the terminal screen.
\x0c=Mod4+k
# Send Shift+Return as CSI-u so TUIs can distinguish it from Return.
\x1b[13;2u=Shift+Return
# Send Alt+Shift+Return as CSI-u so tmux can match M-S-Enter.
\x1b[13;4u=Mod1+Shift+Return
```

## 完整相关配置片段

```ini
[main]
include=~/.local/state/omarchy/current/theme/foot.ini
term=xterm-256color
font=JetBrainsMono Nerd Font:size=9
pad=14x14
initial-window-mode=windowed
workers=0

[scrollback]
lines=10000
multiplier=7.0

[cursor]
style=block
blink=no

[key-bindings]
clipboard-copy=Control+Insert Control+Shift+c XF86Copy
primary-paste=none
clipboard-paste=Shift+Insert Control+Shift+v XF86Paste
```
