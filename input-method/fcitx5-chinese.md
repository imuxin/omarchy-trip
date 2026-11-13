# Fcitx5 中文输入法安装与切换为 WowType

## 1. 基础框架与演进记录

系统采用 `fcitx5`（`5.1.23`）作为输入法框架，并由 Omarchy 的用户级 systemd 服务 `omarchy-fcitx5.service` 托管（`/usr/bin/fcitx5 --replace --disable notificationitem`）。

- **初期方案**：使用官方仓库的 `fcitx5-chinese-addons` + `fcitx5-pinyin-zhwiki`（普通拼音 + 维基词库）。
- **当前方案**：已切换为 [Gloridust/WowType](https://github.com/Gloridust/WowType)（`0.3.12-1`，支持整句、容错、简拼、中英混打、本地词频学习、离线语音与图形化设置），并已卸载旧的 `fcitx5-chinese-addons` 与 `fcitx5-pinyin-zhwiki`。

---

## 2. 当前已安装的输入法相关包

| 软件包 | 作用 |
| :--- | :--- |
| `fcitx5` / `fcitx5-configtool` | Fcitx5 核心框架与图形化配置工具 |
| `fcitx5-gtk` / `fcitx5-qt` | GTK 与 Qt5/Qt6 应用输入法前端支持 |
| `wowtype` | WowType 中文拼音引擎插件与词库（`0.3.12-1`） |
| `python-gobject` / `gtk4` / `libadwaita` | 「WowType 设置」图形界面运行依赖 |

### 安装 / 升级命令

```bash
curl -fsSL https://raw.githubusercontent.com/Gloridust/WowType/main/install.sh | bash
```

### 卸载原拼音包（可选）

```bash
sudo pacman -Rns fcitx5-chinese-addons fcitx5-pinyin-zhwiki
```

---

## 3. Fcitx5 配置文件

### 输入法列表（`~/.config/fcitx5/profile`）
仅保留英文键盘 `keyboard-us` 与 `wowtype`，使 `Cmd/Super + Space` 直接在中英文间二选一切换：

```ini
[Groups/0]
Name=Default
Default Layout=us
DefaultIM=wowtype

[Groups/0/Items/0]
Name=keyboard-us

[Groups/0/Items/1]
Name=wowtype

[GroupOrder]
0=Default
```

> 注：手动修改 `~/.config/fcitx5/profile` 前需先 `systemctl --user stop omarchy-fcitx5.service`，改完再 `start`，避免运行中的 `fcitx5` 在退出或自动保存时覆盖文件。

### 主配置与外观
- `~/.config/fcitx5/config`：`ActiveByDefault=False`
- `~/.config/fcitx5/conf/classicui.conf`：`Theme=omarchy`、`DarkTheme=omarchy`
- `~/.config/fcitx5/conf/wayland.conf` & `xcb.conf`：`Allow Overriding System XKB Settings=False`

---

## 4. 快捷键联动与服务管理

- **切换输入法**：Hyprland 覆盖 `~/.config/hypr/bindings.lua` 将 `SUPER + SPACE` 绑定为 `fcitx5-remote -t`（详见 [Hyprland 快捷键覆盖](../keybindings/hyprland-overrides.md)）。
- **重启输入法服务**：

  ```bash
  systemctl --user restart omarchy-fcitx5.service
  ```

- **验证命令**：

  ```bash
  fcitx5-remote -n
  wowtype-check --lexicon /usr/share/wowtype/lexicon.tsv
  wowtype-show --lexicon /usr/share/wowtype/lexicon.tsv nihao
  ```

> **提示**：重启 `fcitx5` 后，Chrome、VS Code、微信等 Chromium / Electron 应用需彻底退出并重新打开，才能重新连接输入法。
