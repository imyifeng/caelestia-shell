> [!NOTE]
> 本仓库为**个人自用修改版**，在上游 [caelestia-dots/shell](https://github.com/caelestia-dots/shell) 的基础上进行了个人定制，文档均已汉化。
> 因此与上游可能存在差异，通用功能与更新请以上游仓库为准。

<h1 align=center>caelestia-shell</h1>

<div align=center>

![GitHub last commit](https://img.shields.io/github/last-commit/caelestia-dots/shell?style=for-the-badge&labelColor=101418&color=9ccbfb)
![GitHub Repo stars](https://img.shields.io/github/stars/caelestia-dots/shell?style=for-the-badge&labelColor=101418&color=b9c8da)
![GitHub repo size](https://img.shields.io/github/repo-size/caelestia-dots/shell?style=for-the-badge&labelColor=101418&color=d3bfe6)
[![Ko-Fi donate](https://img.shields.io/badge/donate-kofi?style=for-the-badge&logo=ko-fi&logoColor=ffffff&label=ko-fi&labelColor=101418&color=f16061&link=https%3A%2F%2Fko-fi.com%2Fsoramane)](https://ko-fi.com/soramane)
[![Discord invite](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fdiscordapp.com%2Fapi%2Finvites%2FBGDCFCmMBk%3Fwith_counts%3Dtrue&query=approximate_member_count&style=for-the-badge&logo=discord&logoColor=ffffff&label=discord&labelColor=101418&color=96f1f1&link=https%3A%2F%2Fdiscord.gg%2FBGDCFCmMBk)][discord]

</div>

https://github.com/user-attachments/assets/0840f496-575c-4ca6-83a8-87bb01a85c5f

## 组件

- 界面组件：[`Quickshell`](https://quickshell.outfoxxed.me)
- 窗口管理器：[`Hyprland`](https://hypr.land)
- 配置文件：[`caelestia`][dots-repo]

## 安装

> [!NOTE]
> 本仓库仅包含 Caelestia 的桌面 shell。如果你想了解整个 dotfiles（内含本 shell）
> 的安装说明，请前往[主仓库][dots-repo]。

### Arch Linux

> [!WARNING]
> 如果你想对 shell 做自己的修改/调整，请**不要**直接编辑 AUR 软件包安装的文件，
> 而是按照[手动安装](#手动安装)一节的说明操作。

Shell 已发布到 AUR，包名为 `caelestia-shell`。推荐使用 AUR 助手安装，
例如 [`paru`](https://github.com/morganamilo/paru)，也可以手动下载 PKGBUILD 后执行 `makepkg -si`。

AUR 中还有跟随最新提交的 `caelestia-shell-git` 包。它处于最前沿，可能不稳定或有 bug。
普通用户建议使用稳定包（`caelestia-shell`）。

### Nix

可以通过 `nix run` 直接运行 shell：

```sh
nix run github:caelestia-dots/shell#with-cli
```

或将其加入你的系统配置：

```nix
{
  inputs = {
    nixpkgs.url = "github:nixos/nixpkgs/nixos-unstable";

    caelestia-shell = {
      url = "github:caelestia-dots/shell";
      inputs.nixpkgs.follows = "nixpkgs";
    };
  };
}
```

要获得完整功能，请使用 `caelestia-shell.packages.<system>.with-cli`，它可以加入
`environment.systemPackages`、`users.users.<username>.packages`，使用 home-manager 时加入 `home.packages`，
或加入 devshell。`default` 包不包含 CLI。
之后即可使用 `caelestia-shell` 启动 shell。

对于 home-manager 用户，还可以使用 Caelestia 的 Home Manager 模块（见[配置](#home-manager-模块)一节），
它会安装并配置 shell 和 CLI。

### 手动安装

依赖：

- [`caelestia-cli`](https://github.com/caelestia-dots/cli)
- [`quickshell-git`](https://git.outfoxxed.me/quickshell/quickshell) - 必须使用 git 版本，而非最新的正式tag版本
- `glibc`
- `gcc-libs`
- [`ddcutil`](https://github.com/rockowitz/ddcutil)
- [`brightnessctl`](https://github.com/Hummer12007/brightnessctl)
- [`libcava`](https://github.com/LukashonakV/cava)
- [`networkmanager`](https://gitlab.freedesktop.org/NetworkManager/NetworkManager)
- [`lm_sensors`](https://github.com/lm-sensors/lm-sensors)
- [`aubio`](https://github.com/aubio/aubio)
- [`libpipewire`](https://github.com/PipeWire/pipewire)
- [`libqalculate`](https://github.com/Qalculate/libqalculate)
- [`power-profiles-daemon`](https://gitlab.freedesktop.org/upower/power-profiles-daemon)
- [`ttf-material-symbols-variable`](https://github.com/google/material-design-icons)
- [`ttf-rubik-vf`](https://github.com/googlefonts/rubik)
- [`ttf-cascadia-code-nerd`](https://github.com/ryanoasis/nerd-fonts)
- `qt6-base`
- `qt6-declarative`
- `qt6-imageformats`
- [`qt6-m3shapes-git`](https://github.com/soramanew/m3shapes)
- [`swappy`](https://github.com/jtheoof/swappy)
- [`fish`](https://github.com/fish-shell/fish-shell)
- [`bash`](https://www.gnu.org/software/bash)

构建依赖：

- [`cmake`](https://gitlab.kitware.com/cmake/cmake)
- [`ninja`](https://github.com/ninja-build/ninja)
- `qt6-shadertools`

> [!IMPORTANT]
> 下面的命令（以及“更新”一节中的命令）假设已设置 `$XDG_CONFIG_HOME`。
> 如果未设置，请将其替换为你的配置目录路径（通常是 `~/.config`）。

手动安装 shell 时，请先安装所有依赖，然后将本仓库克隆到 `$XDG_CONFIG_HOME/quickshell/caelestia`，
再使用 CMake 构建并安装。

```sh
cd $XDG_CONFIG_HOME/quickshell
git clone https://github.com/caelestia-dots/shell.git caelestia

cd caelestia
cmake -B build -G Ninja -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/
cmake --build build
sudo cmake --install build
```

> [!TIP]
> 你可以通过 CMake 开关 `INSTALL_LIBDIR`、`INSTALL_QMLDIR` 和 `INSTALL_QSCONFDIR`
> 自定义库（例如版本助手）、QML 插件和 Quickshell 配置目录的安装位置。如果设置了
> `INSTALL_LIBDIR` 开关，还必须在系统环境变量中把 `CAELESTIA_LIB_DIR` 变量设置为同一目录。
>
> 例如，安装到 `~/.config/quickshell/caelestia` 以便本地修改：
>
> ```sh
> mkdir -p ~/.config/quickshell/caelestia
> cmake -B build -G Ninja -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/ -DINSTALL_QSCONFDIR="$HOME/.config/quickshell/caelestia"
> cmake --build build
> sudo cmake --install build
> sudo chown -R $USER ~/.config/quickshell/caelestia
> ```

## 用法

可以通过 `caelestia shell -d`（推荐）或 `qs -c caelestia -n -d` 启动 shell。
如有需要，可以省略 `-d` 让 shell 附加在当前终端上运行，
不过你多半希望它以守护进程方式运行（这样关闭终端时 shell 不会退出）。

如果使用 [Caelestia dotfiles][dots-repo]，shell 会在登录时通过 Hyprland 配置中的
`hl.on("hyprland.start", ...)` 函数自动启动。

### 快捷键 / IPC

所有快捷键都可以通过 Hyprland [全局快捷键](https://wiki.hypr.land/Configuring/Basics/Binds/#dbus-global-shortcuts)访问。
如果使用 [Caelestia dotfiles][dots-repo]，这些快捷键已经为你配置好了。
否则，可以参考 [`keybinds.lua`](https://github.com/caelestia-dots/caelestia/blob/main/hypr/hyprland/keybinds.lua#L63-L67) 文件中
使用全局快捷键的示例。

所有 IPC 命令都可以通过 `caelestia shell ...` 访问，例如：

```sh
caelestia shell mpris getActive trackTitle
```

运行 `caelestia shell -s` 可以查看可用 IPC 命令列表。

### 头像 / 壁纸

仪表盘的头像读取自 `~/.face` 文件。你可以在仪表盘中点击头像进行设置，
也可以手动把图片复制或符号链接到该路径。

壁纸切换器的壁纸默认读取自 `~/Pictures/Wallpapers`。如需更改，
请修改 `~/.config/caelestia/shell.json` 中的 `paths.wallpaperDir`。

要设置壁纸，可以在启动器中输入 `>wallpaper` 打开壁纸切换器。
此外，也可以使用 `caelestia wallpaper -f <壁纸路径>` 直接设置壁纸。
使用 `caelestia wallpaper -h` 可以查看此命令的更多信息。

## 更新

### 软件包安装（AUR）

如果使用完整的 dotfiles 或 CLI，运行 `caelestia update` 即可完成系统更新并更新 dots。
如果你只单独安装了 shell，请使用 AUR 助手（如 `paru`）更新系统。

### 手动安装

如果你是通过克隆仓库手动安装的 shell，可以在本地检出中拉取 git 更新。

例如，如果安装到了 `$XDG_CONFIG_HOME/quickshell/caelestia`：

```sh
cd $XDG_CONFIG_HOME/quickshell/caelestia
git pull
```

## 配置

所有配置选项都放在 `~/.config/caelestia/shell.json` 中。此文件默认**不会**创建，需要你手动创建。配置文件中未写明的选项将使用各自的默认值。

### 每显示器配置

你可以在 `~/.config/caelestia/monitors/<显示器名>/shell.json` 中配置每显示器的选项。
运行以下命令列出可用显示器的名称：

```sh
hyprctl monitors -j | jq -r '.[].name'
```

这些文件中设置的选项会**覆盖**全局配置中的相应选项。每显示器配置中未出现的选项将继承全局配置中的值。

例如，要让名为 `DP-1` 的显示器自动隐藏状态栏：

**`~/.config/caelestia/monitors/DP-1/shell.json`**

```json
{
    "bar": {
        "persistent": false
    }
}
```

> [!NOTE]
> 并非所有选项都支持每显示器覆盖。请特别注意，以下选项只读取全局配置，
> 会忽略每显示器配置文件中的相应选项。
>
> <details><summary>被忽略的选项</summary>
>
> - `appearance`：`anim.*`、`transparency.*`
> - `bar.tray`：`hiddenIcons`、`iconSubs`
> - `bar.workspaces`：`ignoredTags`、`specialWorkspaceIcons`、`windowIcons`、`workspaceIcons`
> - `dashboard`：`mediaUpdateInterval`、`resourceUpdateInterval`
> - `general`：`apps.*`、`battery.*`、`idle.*`、`logo`
> - `launcher`：`actionPrefix`、`actions`、`enableDangerousActions`、`favouriteApps`、`hiddenApps`、`specialPrefix`、`useFuzzy.*`、`vimKeybinds`
> - `lock`：`enableFprint`、`enableHowdy`、`maxFprintTries`、`maxHowdyTries`、`triggerHowdyOnWake`
> - `nexus`：`networkRescanInterval`
> - `notifs`：`actionOnClick`、`defaultExpireTimeout`、`expire`、`fullscreen`、`fullscreenExpireTimeout`
> - `paths`：`lyricsDir`、`wallpaperDir`
> - `services`：`audioIncrement`、`brightnessIncrement`、`clockFormat`、`dataUnits`、`defaultPlayer`、`gpuType`、`lyricsBackend`、`maxVolume`、`playerAliases`、`sensorUnits`、`smartScheme`、`visualiserBars`、`weatherLocation`、`weatherUnits`
> - `utilities`：`toasts.*`、`vpn.*`
>
> </details>

### 示例配置

> [!WARNING]
> 示例配置包含 `shell.json` 的**全部**配置选项。**不**建议将这份完整配置
> 复制粘贴到 `shell.json` 中，因为选项或其默认值可能随版本更新而变化，导致配置过时。
>
> 此示例仅用于展示所有可用选项，你应该<ins>只添加自己想修改的选项</ins>。

<details><summary>示例配置</summary>

```json
{
    "enabled": true,
    "appearance": {
        "deformScale": 1,
        "rounding": {
            "scale": 1
        },
        "spacing": {
            "scale": 1
        },
        "padding": {
            "scale": 1
        },
        "font": {
            "scale": 1,
            "clock": "Rubik",
            "workspaces": "Rubik",
            "headline": {
                "family": "GoogleSansFlex",
                "large": {
                    "size": 32,
                    "weight": 500,
                    "italic": false,
                    "vaxes": { "ROND": 25 }
                },
                "medium": {
                    "size": 28,
                    "weight": 500,
                    "italic": false,
                    "vaxes": { "ROND": 25 }
                },
                "small": {
                    "size": 24,
                    "weight": 500,
                    "italic": false,
                    "vaxes": { "ROND": 25 }
                }
            },
            "title": {
                "family": "GoogleSansFlex",
                "large": {
                    "size": 22,
                    "weight": 500,
                    "italic": false,
                    "vaxes": { "ROND": 25 }
                },
                "medium": {
                    "size": 16,
                    "weight": 500,
                    "italic": false,
                    "vaxes": { "ROND": 25 }
                },
                "small": {
                    "size": 14,
                    "weight": 500,
                    "italic": false,
                    "vaxes": { "ROND": 25 }
                }
            },
            "body": {
                "family": "GoogleSansFlex",
                "large": {
                    "size": 16,
                    "weight": 400,
                    "italic": false,
                    "vaxes": { "ROND": 25 }
                },
                "medium": {
                    "size": 14,
                    "weight": 400,
                    "italic": false,
                    "vaxes": { "ROND": 25 }
                },
                "small": {
                    "size": 12,
                    "weight": 400,
                    "italic": false,
                    "vaxes": { "ROND": 25 }
                }
            },
            "label": {
                "family": "GoogleSansFlex",
                "large": {
                    "size": 14,
                    "weight": 500,
                    "italic": false,
                    "vaxes": { "ROND": 25 }
                },
                "medium": {
                    "size": 12,
                    "weight": 500,
                    "italic": false,
                    "vaxes": { "ROND": 25 }
                },
                "small": {
                    "size": 11,
                    "weight": 400,
                    "italic": false,
                    "vaxes": { "ROND": 25 }
                }
            },
            "mono": {
                "family": "CaskaydiaCove NF",
                "large": {
                    "size": 16,
                    "weight": 400,
                    "italic": false,
                    "vaxes": {}
                },
                "medium": {
                    "size": 14,
                    "weight": 400,
                    "italic": false,
                    "vaxes": {}
                },
                "small": {
                    "size": 12,
                    "weight": 400,
                    "italic": false,
                    "vaxes": {}
                }
            },
            "icon": {
                "family": "Material Symbols Rounded",
                "extraLarge": {
                    "size": 36,
                    "weight": 400,
                    "italic": false,
                    "vaxes": {}
                },
                "large": {
                    "size": 24,
                    "weight": 400,
                    "italic": false,
                    "vaxes": {}
                },
                "medium": {
                    "size": 18,
                    "weight": 400,
                    "italic": false,
                    "vaxes": {}
                },
                "small": {
                    "size": 15,
                    "weight": 400,
                    "italic": false,
                    "vaxes": {}
                }
            }
        },
        "anim": {
            "durations": {
                "scale": 1
            }
        },
        "transparency": {
            "enabled": false,
            "base": 0.85,
            "layers": 0.4
        }
    },
    "general": {
        "logo": "",
        "showOverFullscreen": false,
        "mediaGifSpeedAdjustment": 300,
        "sessionGifSpeed": 0.7,
        "apps": {
            "terminal": ["kitty"],
            "audio": ["pwvucontrol"],
            "playback": ["mpv"],
            "explorer": ["thunar"]
        },
        "idle": {
            "lockBeforeSleep": true,
            "inhibitWhenAudio": true,
            "inhibitWhenCharging": false,
            "timeouts": [
                {
                    "timeout": 180,
                    "idleAction": "lock",
                    "inhibitWhenAudio": false,
                    "inhibitWhenCharging": false,
                    "respectInhibitors": true
                },
                {
                    "timeout": 300,
                    "idleAction": "dpms off",
                    "returnAction": "dpms on"
                },
                {
                    "timeout": 600,
                    "idleAction": ["suspendThenHibernate"]
                }
            ]
        },
        "battery": {
            "warnLevels": [
                {
                    "level": 20,
                    "title": "Low battery",
                    "message": "You might want to plug in a charger",
                    "icon": "battery_android_frame_2"
                },
                {
                    "level": 10,
                    "title": "Did you see the previous message?",
                    "message": "You should probably plug in a charger <b>now</b>",
                    "icon": "battery_android_frame_1"
                },
                {
                    "level": 5,
                    "title": "Critical battery level",
                    "message": "PLUG THE CHARGER RIGHT NOW!!",
                    "icon": "battery_android_alert",
                    "critical": true
                }
            ],
            "criticalLevel": 3
        }
    },
    "background": {
        "enabled": true,
        "wallpaperEnabled": true,
        "desktopClock": {
            "enabled": false,
            "scale": 1.0,
            "position": "bottom-right",
            "invertColors": false,
            "background": {
                "enabled": false,
                "opacity": 0.7,
                "blur": true
            },
            "shadow": {
                "enabled": true,
                "opacity": 0.7,
                "blur": 0.4
            }
        },
        "visualiser": {
            "enabled": false,
            "autoHide": true,
            "blur": false,
            "rounding": 1,
            "spacing": 1
        }
    },
    "bar": {
        "persistent": true,
        "showOnHover": true,
        "dragThreshold": 20,
        "scrollActions": {
            "workspaces": true,
            "volume": true,
            "brightness": true
        },
        "popouts": {
            "activeWindow": true,
            "tray": true,
            "statusIcons": true
        },
        "workspaces": {
            "shown": 5,
            "activeIndicator": true,
            "occupiedBg": false,
            "showUnoccupied": true,
            "perMonitor": true,
            "showWindows": true,
            "showWindowsOnSpecialWorkspaces": true,
            "maxWindowIcons": 5,
            "activeTrail": false,
            "displayType": "shapes",
            "specialDisplayType": "icons",
            "label": "  ",
            "occupiedLabel": "󰮯",
            "activeLabel": "󰮯",
            "capitalisation": "preserve",
            "workspaceIcons": [],
            "specialWorkspaceIcons": [
                {
                    "name": "special",
                    "icon": "star"
                },
                {
                    "name": "communication",
                    "icon": "forum"
                },
                {
                    "name": "music",
                    "icon": "music_cast"
                },
                {
                    "name": "todo",
                    "icon": "checklist"
                },
                {
                    "name": "sysmon",
                    "icon": "monitor_heart"
                }
            ],
            "ignoredTags": ["hide_in_bar", "xwl_popup"],
            "windowIcons": [
                {
                    "regex": "steam(_app_(default|[0-9]+))?",
                    "icon": "sports_esports"
                }
            ]
        },
        "activeWindow": {
            "compact": false,
            "inverted": false,
            "showOnHover": true
        },
        "tray": {
            "background": false,
            "recolour": false,
            "compact": false,
            "iconSubs": [],
            "hiddenIcons": []
        },
        "clock": {
            "background": false,
            "showDate": false,
            "showIcon": true
        },
        "statusIcons": [
            {
                "id": "lockStatus",
                "enabled": true
            },
            {
                "id": "audio",
                "enabled": false
            },
            {
                "id": "microphone",
                "enabled": false
            },
            {
                "id": "kbLayout",
                "enabled": false
            },
            {
                "id": "network",
                "enabled": true
            },
            {
                "id": "bluetooth",
                "enabled": true
            },
            {
                "id": "battery",
                "enabled": true
            }
        ],
        "entries": [
            {
                "id": "logo",
                "enabled": true
            },
            {
                "id": "workspaces",
                "enabled": true
            },
            {
                "id": "spacer",
                "enabled": true
            },
            {
                "id": "activeWindow",
                "enabled": true
            },
            {
                "id": "spacer",
                "enabled": true
            },
            {
                "id": "tray",
                "enabled": true
            },
            {
                "id": "clock",
                "enabled": true
            },
            {
                "id": "statusIcons",
                "enabled": true
            },
            {
                "id": "power",
                "enabled": true
            }
        ],
        "excludedScreens": []
    },
    "border": {
        "thickness": 10,
        "rounding": 25,
        "smoothing": 20
    },
    "dashboard": {
        "enabled": true,
        "showOnHover": true,
        "showDashboard": true,
        "showMedia": true,
        "showPerformance": true,
        "showWeather": true,
        "mediaUpdateInterval": 500,
        "resourceUpdateInterval": 1000,
        "dragThreshold": 50,
        "performance": {
            "showBattery": true,
            "showGpu": true,
            "showCpu": true,
            "showMemory": true,
            "showStorage": true,
            "showNetwork": true
        }
    },
    "launcher": {
        "enabled": true,
        "showOnHover": false,
        "maxShown": 7,
        "maxWallpapers": 9,
        "specialPrefix": "@",
        "actionPrefix": ">",
        "enableDangerousActions": false,
        "dragThreshold": 50,
        "vimKeybinds": false,
        "favouriteApps": [],
        "hiddenApps": [],
        "useFuzzy": {
            "apps": false,
            "actions": false,
            "schemes": false,
            "variants": false,
            "wallpapers": false
        },
        "actions": [
            {
                "name": "Calculator",
                "icon": "calculate",
                "description": "Do simple math equations (powered by Qalc)",
                "command": ["autocomplete", "calc"],
                "enabled": true,
                "dangerous": false
            },
            {
                "name": "Scheme",
                "icon": "palette",
                "description": "Change the current colour scheme",
                "command": ["autocomplete", "scheme"],
                "enabled": true,
                "dangerous": false
            },
            {
                "name": "Wallpaper",
                "icon": "image",
                "description": "Change the current wallpaper",
                "command": ["autocomplete", "wallpaper"],
                "enabled": true,
                "dangerous": false
            },
            {
                "name": "Variant",
                "icon": "colors",
                "description": "Change the current scheme variant",
                "command": ["autocomplete", "variant"],
                "enabled": true,
                "dangerous": false
            },
            {
                "name": "Random",
                "icon": "casino",
                "description": "Switch to a random wallpaper",
                "command": ["caelestia", "wallpaper", "-r"],
                "enabled": true,
                "dangerous": false
            },
            {
                "name": "Light",
                "icon": "light_mode",
                "description": "Change the scheme to light mode",
                "command": ["setMode", "light"],
                "enabled": true,
                "dangerous": false
            },
            {
                "name": "Dark",
                "icon": "dark_mode",
                "description": "Change the scheme to dark mode",
                "command": ["setMode", "dark"],
                "enabled": true,
                "dangerous": false
            },
            {
                "name": "Shutdown",
                "icon": "power_settings_new",
                "description": "Shutdown the system",
                "command": ["poweroff"],
                "enabled": true,
                "dangerous": true
            },
            {
                "name": "Reboot",
                "icon": "cached",
                "description": "Reboot the system",
                "command": ["reboot"],
                "enabled": true,
                "dangerous": true
            },
            {
                "name": "Logout",
                "icon": "exit_to_app",
                "description": "Log out of the current session",
                "command": ["logout"],
                "enabled": true,
                "dangerous": true
            },
            {
                "name": "Lock",
                "icon": "lock",
                "description": "Lock the current session",
                "command": ["loginctl", "lock-session"],
                "enabled": true,
                "dangerous": false
            },
            {
                "name": "Sleep",
                "icon": "bedtime",
                "description": "Suspend then hibernate",
                "command": ["suspendThenHibernate"],
                "enabled": true,
                "dangerous": false
            },
            {
                "name": "Settings",
                "icon": "settings",
                "description": "Configure the shell",
                "command": ["caelestia", "shell", "nexus", "open"],
                "enabled": true,
                "dangerous": false
            }
        ]
    },
    "lock": {
        "enabled": true,
        "useWallpaper": false,
        "recolourLogo": true,
        "enableFprint": true,
        "maxFprintTries": 3,
        "enableHowdy": true,
        "maxHowdyTries": 3,
        "triggerHowdyOnWake": true,
        "hideNotifs": false,
        "enableSessionControls": true
    },
    "nexus": {
        "wallpapersPerRow": 4,
        "networkRescanInterval": 15000
    },
    "notifs": {
        "expire": true,
        "fullscreen": "On",
        "defaultExpireTimeout": 5000,
        "fullscreenExpireTimeout": 2000,
        "clearThreshold": 0.3,
        "expandThreshold": 20,
        "actionOnClick": false,
        "groupPreviewNum": 3,
        "openExpanded": false
    },
    "osd": {
        "enabled": true,
        "hideDelay": 2000,
        "enableBrightness": true,
        "enableMicrophone": false
    },
    "services": {
        "weatherLocation": "",
        "weatherUnits": "Auto",
        "sensorUnits": "Celsius",
        "dataUnits": "Binary",
        "clockFormat": "Auto",
        "gpuType": "Auto",
        "visualiserBars": 60,
        "audioIncrement": 0.1,
        "brightnessIncrement": 0.1,
        "maxVolume": 1.0,
        "smartScheme": true,
        "defaultPlayer": "Spotify",
        "playerAliases": [
            { "from": "com.github.th_ch.youtube_music", "to": "YT Music" }
        ],
        "lyricsBackend": "Auto"
    },
    "session": {
        "enabled": true,
        "dragThreshold": 30,
        "vimKeybinds": false,
        "icons": {
            "logout": "logout",
            "shutdown": "power_settings_new",
            "hibernate": "downloading",
            "reboot": "cached"
        },
        "commands": {
            "logout": ["logout"],
            "shutdown": ["poweroff"],
            "hibernate": ["hibernate"],
            "reboot": ["reboot"]
        }
    },
    "sidebar": {
        "enabled": true,
        "showOnHover": false,
        "minHoverThreshold": 200,
        "dragThreshold": 80
    },
    "utilities": {
        "enabled": true,
        "maxToasts": 4,
        "toasts": {
            "fullscreen": "off",
            "configLoaded": true,
            "chargingChanged": true,
            "gameModeChanged": true,
            "dndChanged": true,
            "audioOutputChanged": true,
            "audioInputChanged": true,
            "capsLockChanged": true,
            "numLockChanged": true,
            "kbLayoutChanged": true,
            "kbLimit": true,
            "vpnChanged": true,
            "nowPlaying": false
        },
        "vpn": {
            "enabled": false,
            "provider": [
                {
                    "name": "wireguard",
                    "interface": "your-connection-name",
                    "displayName": "Wireguard (Your VPN)",
                    "enabled": false
                }
            ]
        },
        "quickToggles": [
            {
                "id": "wifi",
                "enabled": true
            },
            {
                "id": "bluetooth",
                "enabled": true
            },
            {
                "id": "mic",
                "enabled": true
            },
            {
                "id": "settings",
                "enabled": true
            },
            {
                "id": "gameMode",
                "enabled": true
            },
            {
                "id": "dnd",
                "enabled": true
            },
            {
                "id": "vpn",
                "enabled": false
            }
        ]
    },
    "paths": {
        "wallpaperDir": "~/Pictures/Wallpapers",
        "lyricsDir": "~/Music/lyrics/",
        "sessionGif": "root:/assets/kurukuru.gif",
        "mediaGif": "root:/assets/bongocat.gif",
        "noNotifsPic": "root:/assets/dino.png",
        "lockNoNotifsPic": "root:/assets/dino.png"
    }
}
```

</details>

### 高级配置

> [!CAUTION]
> 除非你清楚自己在做什么，否则**不要**修改这些选项。这些选项控制 shell 内部使用的 token，
> 修改不当可能导致视觉问题。可用选项可能随版本变动或被移除，恕不另行通知。

单独的 `~/.config/caelestia/shell-tokens.json` 文件允许在不改动 shell 源码的情况下编辑内部 token。
这些 token 影响视觉元素的尺寸和外观，包括各自的圆角、间距、内边距、字号、动画时长与曲线，
以及某些组件的大小。`shell.json` 中的外观缩放值会与这些基础 token 值相乘，得到最终的计算值。

每显示器的 token 覆盖同样可用，位于
`~/.config/caelestia/monitors/<显示器名>/shell-tokens.json`。

### Home Manager 模块

NixOS 用户还可以使用 Home Manager 模块。

<details><summary><code>home.nix</code></summary>

```nix
programs.caelestia = {
  enable = true;
  systemd = {
    enable = false; # if you prefer starting from your compositor
    target = "graphical-session.target";
    environment = [];
  };
  settings = {
    bar.statusIcons = [
      { id = "lockStatus"; enabled = true; }
      { id = "network"; enabled = true; }
      { id = "bluetooth"; enabled = true; }
      { id = "battery"; enabled = false; }
    ];
    paths.wallpaperDir = "~/Images";
  };
  cli = {
    enable = true; # Also add caelestia-cli to path
    settings = {
      theme.enableGtk = false;
    };
  };
};
```

该模块会自动将 shell 添加到 path，并具备**完整功能**。CLI 不是必需的；不过你也可以选择启用并配置它。

</details>

## 常见问题

### 需要帮助或支持？

欢迎加入 Caelestia 的 Discord 服务器获取帮助和交流，地址见[这里][discord]。

### 我想自己修改 Hyprland 配置！

请查看 [dots 仓库](https://github.com/caelestia-dots/caelestia#configuring)的配置（Configuring）一节。

### 我想修改其他东西！

请参阅对应仓库的[手动安装](#手动安装)一节。

### 我想禁用某个功能！

请阅读[配置](#配置)一节。
如果没有对应的选项，可以提交[功能请求](https://github.com/caelestia-dots/shell/issues/new?template=feature.yml)。

### 如何让配色方案跟随壁纸变化？

通过启动器中的 `>wallpaper` 或 `caelestia wallpaper` 设置壁纸，然后通过
启动器中的 `>scheme` 或 `caelestia scheme set` 将配色方案设为动态方案，例如：

```sh
caelestia wallpaper -f <壁纸路径>
caelestia scheme set -n dynamic
```

### 启动器里看不到我的壁纸！

启动器默认从 `~/Pictures/Wallpapers` 读取壁纸，你可以在配置中修改此路径。此外，
启动器一次只显示奇数张壁纸。如果你只有 2 张壁纸，建议再多放几张
（或者干脆只放一张）。

## 致谢

感谢 Hyprland Discord 社区（尤其是 #rice-discussion 频道里的各位大佬）为改进这些 dots
提供的所有帮助和建议！

特别感谢 [@outfoxxed](https://github.com/outfoxxed) 开发了 Quickshell，并付出大量精力修复问题
以及实现各种功能请求。

再次特别感谢 [@end_4](https://github.com/end-4) 的[配置](https://github.com/end-4/dots-hyprland)，
它对我学习 Quickshell 的用法帮助很大。

最后，还要感谢所有给了我启发的配置（目前只有一个）：

- [Axenide/Ax-Shell](https://github.com/Axenide/Ax-Shell)

## Stonks 📈

<a href="https://www.star-history.com/#caelestia-dots/shell&Date">
    <picture>
        <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/svg?repos=caelestia-dots/shell&type=Date&theme=dark" />
        <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/svg?repos=caelestia-dots/shell&type=Date" />
        <img alt="Star History Chart" src="https://api.star-history.com/svg?repos=caelestia-dots/shell&type=Date" />
    </picture>
</a>

[dots-repo]: https://github.com/caelestia-dots/caelestia
[discord]: https://caelestiashell.com/discord
