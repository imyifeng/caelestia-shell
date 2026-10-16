> [!NOTE]
> 本仓库是 [caelestia-dots/shell](https://github.com/caelestia-dots/shell) 的**个人魔改版**（fork），维护者：imyifeng。
> 上游通用文档（配置选项、每显示器覆盖、shell-tokens 等）请看原仓库 README；本文档描述魔改内容与维护流程。

# caelestia-shell（魔改版）

基于 Quickshell 的 Caelestia 桌面 shell。魔改三件事：**简体中文全量翻译**、**默认值调整**、**Arch 打包**。

## 与上游的差异

| 项 | 上游 | 本仓库 |
| --- | --- | --- |
| 界面语言 | 仅英文（i18n 框架已内置，无任何语言目录） | **zh_CN 全量翻译**：821 条，`plugin/src/Caelestia/I18n/trs/zh_CN.po`，已注册进 CMake 构建 |
| 启动器计算器 | 拉起 `fish -C` | 拉起 `zsh -c`（`modules/launcher/items/CalcItem.qml`） |
| 默认终端 | foot | kitty（`plugin/src/Caelestia/Config/generalconfig.hpp`） |
| 打包 | 无 | `aur/PKGBUILD` → 包名 `caelestia-shell-imyifeng`，与官方包互斥 |

**翻译的两条铁律**（改翻译前必读）：

1. **msgstr 绝不能留空**——本项目的翻译器（`I18n/translator.cpp` 自研 .mo 解析）对空翻译返回空字符串，界面会直接渲染成空白。不翻译的条目 msgstr 原样复制 msgid。
2. **启动器 `>` 命令的动作名保持英文**——`msgctxt "launcher action"` 的 13 条（Shutdown/Reboot/Scheme/Wallpaper…）是用户要敲的命令，翻译了就得切输入法打命令。

另：`units`（°C、KiB 等）、`REC`、锁屏 fetch 的等宽对齐标签也保持原文。

## 汉化维护流程

上游更新带来新字符串后：

```sh
./scripts/trs.fish raw        # 重新提取 → plugin/src/Caelestia/I18n/trs/raw.pot
./scripts/trs.fish zh_CN      # 合并进现有 zh_CN.po（新条目 msgstr 为空）
# 翻译新条目（必须补齐，空=空白；实在不想翻的条目直接删掉整个条目块也行）
msgfmt --check-format -o /dev/null plugin/src/Caelestia/I18n/trs/zh_CN.po   # 校验
cmake --build build           # 重编译（.mo 在编译期嵌入 libcaelestia-i18n.so）
```

- 构建期依赖：`fish gettext`（`trs.fish` 调 xgettext/msgfmt）——这就是 makedepends 里有 fish 的原因
- 覆盖率目检：`CAELESTIA_TEST_TRS=ON` 配置项会生成 test.mo，把所有字符串包成 `[[ ... ]]`
- 语言选择：设置 → Language & region → UI language（或 `shell.json` 的 `general.language`，留空跟随系统 locale）；切换即时生效
- **注意**：shell 配置 `shell.json` 本身不支持热加载，改完要重启 shell

## 打包与安装（`aur/PKGBUILD`）

```sh
cd aur && makepkg -f
sudo pacman -U caelestia-shell-imyifeng-*.pkg.tar.zst   # 冲突提示选 yes，自动替换官方包
```

- 包名 `caelestia-shell-imyifeng`，`conflicts=('caelestia-shell' 'caelestia-shell-git')`、`provides=('caelestia-shell')`
- 版本号 `2.5.0.r<提交数>.g<短SHA>` 自动生成（fork 无 tag 也有兜底）
- 安装布局与官方 AUR 包逐文件一致（`/usr/lib/qt6/qml/Caelestia` + `/etc/xdg/quickshell/caelestia`），已用 fakeroot diff 验证
- 生成物（src/、pkg/、*.pkg.tar.zst、源码克隆目录）均已 gitignore

**构建的两个坑**（PKGBUILD 已处理，手动 cmake 时注意）：

1. 本 fork 没有 tag，`git describe` 拿不到版本 → 必须显式传 `-DVERSION=<纯数字版本>`（如 `2.5.0`）
2. CMake `project(VERSION)` 只接受纯数字，带 `.rN.gSHA` 的完整版本号会配置失败

## 已知问题：SDDM 注销黑屏（已修复，勿回退）

**因果链**：shell 的注销默认走 logind `Session.Terminate` → logind 对整个 session scope 广播 SIGTERM → hyprland 0.55+ 自带的 `start-hyprland` 看门狗在 waitpid 中被信号打断直接 ABRT（有 coredump）→ sddm-helper 上报 `crashed (exit code 1)` → SDDM 视为内部错误**不再重启 greeter** → VT2 留裸 X server（黑屏 + X 光标）、VT1 断信号。另有 Hyprland #5134 一类问题：鼠标触发的 `hyprctl dispatch exit` 会挂起等按键，表现为"点注销没反应"。

**修复方案**（配置在 dots 侧的 `~/.config/caelestia/shell.json`，脚本在 `~/.config/caelestia/logout.sh`）：注销命令指向包装脚本——优先 `hyprshutdown`（官方[extra]仓库）优雅退出 → 超时 8 秒走 `hyprctl dispatch exit` → 仍存活则 `pkill -KILL -u $USER` 强杀整个用户会话，logind 清理后 SDDM 必然重启 greeter。

参考上游讨论：Hyprland #4399、caelestia-dots/shell #177/#964、CachyOS 论坛同款 coredump 报告。

## 手动构建（不用 PKGBUILD 时）

依赖与上游一致（见 `pacman -Qi` 或上游 README），构建期另需 `fish gettext`：

```sh
cmake -B build -G Ninja -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_INSTALL_PREFIX=/usr -DINSTALL_QSCONFDIR=/etc/xdg/quickshell \
    -DVERSION=2.5.0
cmake --build build
sudo cmake --install build
caelestia shell -k; sleep 1; caelestia shell -d
```

装完后会被下次 AUR `caelestia-shell` 更新覆盖——要用 AUR 就别装这个包，要用这个包就别再 AUR 更新，二选一。

## 致谢

上游 [caelestia-dots/shell](https://github.com/caelestia-dots/shell) 及其作者 Soramane、[Quickshell](https://quickshell.outfoxxed.me)（outfoxxed）、Hyprland 社区；i18n 框架为上游 #1966/#1985 的成果。
