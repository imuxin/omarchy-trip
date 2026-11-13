# Fcitx5 中文输入法安装与配置

## 1. 已安装的输入法软件包

通过 Arch 官方仓库安装的 `fcitx5` 及相关组件（可通过 `pacman -Qs fcitx` 查看）：

| 软件包 | 作用 |
| :--- | :--- |
| `fcitx5` | Fcitx5 核心框架 |
| `fcitx5-configtool` | 图形化配置工具（`fcitx5-configtool`） |
| `fcitx5-gtk` / `fcitx5-qt` | GTK 与 Qt5/Qt6 应用输入法前端支持 |
| `fcitx5-chinese-addons` | 中文输入法插件集（含拼音 `pinyin`、双拼、繁简转换等） |
| `fcitx5-pinyin-zhwiki` | 中文维基百科拼音词库 |
| `fcitx5-chewing` / `fcitx5-hangul` / `fcitx5-mozc` | 系统同时存在的新酷音/韩文/日文引擎 |

常用安装命令参考：

```bash
sudo pacman -S fcitx5-im fcitx5-chinese-addons fcitx5-pinyin-zhwiki
```

---

## 2. 输入法列表与核心配置

### 输入法组配置（`~/.config/fcitx5/profile`）
默认输入法组包含英文键盘与拼音：

```ini
[Groups/0]
Name=Default
Default Layout=us
DefaultIM=pinyin

[Groups/0/Items/0]
Name=keyboard-us

[Groups/0/Items/1]
Name=pinyin

[GroupOrder]
0=Default
```

### 主配置（`~/.config/fcitx5/config`）

```ini
[Hotkey/TriggerKeys]
0=Zenkaku_Hankaku
1=Hangul

[Behavior]
ActiveByDefault=False
```

### 外观与 XKB 集成
- `~/.config/fcitx5/conf/classicui.conf`：跟随 Omarchy 主题
  ```ini
  Theme=omarchy
  DarkTheme=omarchy
  ```
- `~/.config/fcitx5/conf/wayland.conf` & `~/.config/fcitx5/conf/xcb.conf`：
  ```ini
  Allow Overriding System XKB Settings=False
  ```

---

## 3. 使用 `Cmd/Super + Space` 切换输入法

不依赖 Fcitx5 自带的 `TriggerKeys`（避免和终端/系统快捷键冲突），而是由 Hyprland 统一通过 `fcitx5-remote -t` 切换中英文输入法：

在 `~/.config/hypr/bindings.lua` 中配置（完整说明见 [Hyprland 快捷键覆盖](../keybindings/hyprland-overrides.md)）：

```lua
hl.unbind("SUPER + SPACE")
hl.unbind("CTRL + SPACE")
o.bind("SUPER + SPACE", "Toggle input method", "fcitx5-remote -t")
o.bind("CTRL + SPACE", "Omarchy menu", "omarchy-menu toggle")
```

> 注：Omarchy 默认还提供 `SUPER + I` / `SUPER + SHIFT + I`（`omarchy-input-method cycle`）用于循环切换输入语言。
