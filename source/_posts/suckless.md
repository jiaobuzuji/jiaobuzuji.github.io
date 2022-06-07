---
title: suckliess
mathjax: true
comments: true
date: 2018-10-12 16:32:01
updated: 2018-10-12 16:32:01
categories:
    - System
    - linux
tags: [linux,suckliess]
---

# 安装

slstatus 的默认安装目录是 /usr/local/，如果你想安装到别的地方，可以在执行下面的安装命令之前编辑 config.mk 文件，把第 7 行左右的 PREFIX 的值改成你想要的目录。这里我选择默认的安装目录。

执行下面的命令来安装 slstatus

cd ~/software/slstatus # 切换到 slstatus 目录
sudo make clean install # 安装

执行完后，slstatus 可执行程序和 slstatus.1 手册都已经被保存在安装目录 (默认 /usr/local/)下。

现在你可以把配置好的显示内容打印到终端上，来查看 slstatus 的效果

slstatus -1 # 只打印一次
slstatus -s # 连续打印，间隔时间是你在 config.h 里设置的时间，按 Ctrl+c 结束打印

把这些信息显示到状态栏上

slstatus &

设置进入图形界面时自动运行 slstatus

在 ~/.xinitrc 中添加 slstatus &，注意一定要添加到 exec 命令的前面，否则 slstatus 不会被执行。
修改 slstatus 配置

安装并启动 slstatus 后，如果你又修改了 config.h 里的配置，可以用下面的命令启动新的 slstatus：

sudo make clean install # 重新编译
killall slstatus # 结束旧的 slstatus 进程
slstatus &

调整音量时及时更新状态栏

用下面的三个脚本来控制音量。

你需要先安装 pamixer (调整音量) 和 xorg-xsetroot (设置状态栏的内容)

sudo pacman -S pamixer xorg-xsetroot

~/.local/bin/vol-up

#!/usr/bin/env bash

# 音量增加 %5
pamixer -i 5

# 更新状态栏
status=$(slstatus -1)
xsetroot -name "$status"

~/.local/bin/vol-down

#!/usr/bin/env bash

# 音量减少 %5
pamixer -d 5

# 更新状态栏
status=$(slstatus -1)
xsetroot -name "$status"

~/.local/bin/vol-toggle

#!/usr/bin/env bash

# 静音和取消静音
pamixer -t

# 更新状态栏
status=$(slstatus -1)
xsetroot -name "$status"

将这三个脚本绑定到快捷键上。如果你用的是 dwm，可以在 dwm 的 config.h 里进行绑定：

```
static const char *voltoggle[] = { "~/.local/bin/vol-toggle",  NULL };
static const char *voldown[] = { "~/.local/bin/vol-down",  NULL };
static const char *volup[]   = { "~/.local/bin/vol-up",  NULL };
...
static Key keys[] = {
/* modifier       key                          function       argument */
{ 0,              XF86XK_AudioMute,            spawn,         {.v = voltoggle } },
{ 0,              XF86XK_AudioLowerVolume,     spawn,         {.v = voldown } },
{ 0,              XF86XK_AudioRaiseVolume,     spawn,         {.v = volup   } },
};
```

你可以执行 xmodmap -pke | less 来查看 XF86XK_AudioMute、XF86XK_AudioLowerVolume 和 XF86XK_AudioRaiseVolume 对应的按键，在我电脑上分别是 F1、F2 和 F3。

重新编译并重启 dwm 后，按 F1、F2 和 F3 键来调整音量，就能看到状态栏的音量信息会及时更新。
卸载

如果你想卸载 slstatus，执行下面的命令：

sudo make uninstall

执行完后，安装目录 (默认 /usr/local/) 下的 slstatus 可执行程序和 slstatus.1 手册都已经被删除。
排错
音量显示 n/a

你可能没有 /dev/mixer 设备，你需要执行 sudo modprobe snd-pcm-oss来加载 snd-pcm-oss 模块。开机自动加载该模块：

sudo echo 'snd-pcm-oss' > /etc/modules-load.d/snd-pcm-oss.conf 
