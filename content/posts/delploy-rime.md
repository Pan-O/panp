+++
title = 'Exclusively Yours : 部署属于个人的输入法方案'
date = 2024-10-15T20:00:00+08:00
toc = true
tags = ["rime", "Linux"，"多端同步"]
+++

作为 cjk 地区的人也许自互联网发展开始便离不开**输入法**，但作为一个输入法，它的用途就表明了由它处理的数据是十分隐私的，当然本文的重点并不在于讨论如何保护个人隐私，只不过如果你在任何论坛问类似这样的问题

>注重隐私应该使用什么输入法？

我相信大多数的回答一定会是[Rime](https://rime.im/)，而隐私只是 Rime 众多优点之一，它开源，多平台，自定义程度高，支持各类输入方案。

## 安装

关于基本的安装可以参考 [oh-my-rime](https://www.mintimate.cc/zh/guide/installRime.html) ，各个平台都有很详细的安装教程，我个人在 Linux 平台下使用的是 fcitx5-rime，Windows 下为小狼毫，而 Android 端为 fcitx5-android，而词库使用的都是[雾凇拼音](https://github.com/iDvel/rime-ice)。

Linux 下我使用的是 arch linux，因此只需要一行命令即可安装完成。这里我使用的是 paru

```
paru -S fcitx5-rime rime-ice
```

词库导入方式也都是下载 Github 上的配置文件，然后将其解压到对应的目录下，这些在 oh-my-rime 的教程里都有详细说明，这里就不做无用废话了

## 个人配置文件

因为 Rime 只是一个输入法**引擎**，实际上在不同平台的实现都是依赖不同的前端，例如 Linux 下的 fcitx，Windows 下的小狼毫。

因此我们便可以做到一份配置文件在多平台使用，大量减少了配置上的负担，即“一端完成，多端享用”

```
patch:
  __include: rime_ice_suggestion:/
  __patch:
    menu/page_size: 6    #每页显示6个候选词
    key_binder/bindings/+:
      - { when: paging, accept: comma, send: Page_Up }    #逗号向上翻页
      - { when: has_menu, accept: period, send: Page_Down }    #句号向上翻页
    ascii_composer/switch_key/Shift_L: noop    #禁用左Shift切换
    schema_list:
      - schema: double_pinyin_flypy    #启用小鹤双拼
```

这是`default.custom.yaml`文件，主要是一些个人的基本设置。

禁用左 Shift 是因为在打游戏的时候，会经常用到左 Shift，导致一直切换输入法，导致游戏体验极差（不得不说 steam 还是很好用的，永劫无间玩的很顺利🤣


```
patch:
  __patch:
    speller:
      algebra:
        - "erase/^xx$/"
        - derive/^([zcs])h/$1/ # zh, ch, sh => z, c, s
        - derive/^([zcs])([^h])/$1h$2/ # z, c, s => zh, ch, sh
        - derive/([aei])n$/$1ng/ # en => eng, in => ing
        - derive/([aei])ng$/$1n/ # eng => en, ing => in
        - derive/([iu])an$/$lang/ # ian => iang, uan => uang
        - derive/([iu])ang$/$lan/ # iang => ian, uang => uan
        - derive/^r/l/                     # r => l
        - "derive/^([jqxy])u$/$1v/"
        - "derive/^([aoe])([ioun])$/$1$1$2/"
        - "xform/^([aoe])(ng)?$/$1$1$2/"
        - "xform/iu$/Ⓠ/"
        - "xform/(.)ei$/$1Ⓦ/"
        - "xform/uan$/Ⓡ/"
        - "xform/[uv]e$/Ⓣ/"
        - "xform/un$/Ⓨ/"
        - "xform/^sh/Ⓤ/"
        - "xform/^ch/Ⓘ/"
        - "xform/^zh/Ⓥ/"
        - "xform/uo$/Ⓞ/"
        - "xform/ie$/Ⓟ/"
        - "xform/(.)i?ong$/$1Ⓢ/"
        - "xform/ing$|uai$/Ⓚ/"
        - "xform/(.)ai$/$1Ⓓ/"
        - "xform/(.)en$/$1Ⓕ/"
        - "xform/(.)eng$/$1Ⓖ/"
        - "xform/[iu]ang$/Ⓛ/"
        - "xform/(.)ang$/$1Ⓗ/"
        - "xform/ian$/Ⓜ/"
        - "xform/(.)an$/$1Ⓙ/"
        - "xform/(.)ou$/$1Ⓩ/"
        - "xform/[iu]a$/Ⓧ/"
        - "xform/iao$/Ⓝ/"
        - "xform/(.)ao$/$1Ⓒ/"
        - "xform/ui$/Ⓥ/"
        - "xform/in$/Ⓑ/"
        - "xlit/ⓆⓌⓇⓉⓎⓊⒾⓄⓅⓈⒹⒻⒼⒽⒿⓀⓁⓏⓍⒸⓋⒷⓃⓂ/qwrtyuiopsdfghjklzxcvbnm/"
        - abbrev/^([a-z]).+$/$1/ #简拼（首字母）
        - abbrev/^([zcs]h).+$/$1/ #简拼（zh, ch, sh）
      alphabet: "zyxwvutsrqponmlkjihgfedcbaZYXWVUTSRQPONMLKJIHGFEDCBA`"
      delimiter: " '"
      initials: zyxwvutsrqponmlkjihgfedcbaZYXWVUTSRQPONMLKJIHGFEDCBA
```

`double_pinyin_flypy.custom.yaml`主要是对小鹤双拼打的模糊音补丁。

并且只有下面这几行是额外添加的，其他几行都是原本配置文件中的内容。之所以这样是因为打补丁去修改配置文件的话，只能选择在末尾添加或者以类似数组下标的形式一行一行插入。

而全部添加在末尾的话则会和双拼的配置发生冲突，可以在[这里](https://gist.github.com/lotem/2320943)的评论区看到很多使用小鹤双拼的人都是选择修改了源文件。
若是以数组下标形式修改的话，后续不好维护，还不如直接把原本的内容复制过来加上。

当然如果你是全拼的话就可以直接追加在末尾。
```
        - derive/^([zcs])h/$1/ # zh, ch, sh => z, c, s
        - derive/^([zcs])([^h])/$1h$2/ # z, c, s => zh, ch, sh
        - derive/([aei])n$/$1ng/ # en => eng, in => ing
        - derive/([aei])ng$/$1n/ # eng => en, ing => in
        - derive/([iu])an$/$lang/ # ian => iang, uan => uang
        - derive/([iu])ang$/$lan/ # iang => ian, uang => uan
        - derive/^r/l/                     # r => l
        xxx
        xxx
        - abbrev/^([a-z]).+$/$1/ #简拼（首字母）
        - abbrev/^([zcs]h).+$/$1/ #简拼（zh, ch, sh）
```

## 多端同步
因为我个人的设备横跨 Linux、Windows、Android 多平台，因此同步便是一道跨不过去的坎。

我个人使用 onedrive 作为储存，在 Windows 和 Linux 下使用 rclone 进行同步，Android 的话则是使用 FolderSync（Android 这边由于配置文件以及同步文件夹只能位于/Android/data/目录下，所以需要支持 SAF 的工具，而 Linux 和 Windows 两端的同步文件夹都可以在`installation.yaml`中设置，具体可以看这个 [issue](https://github.com/fcitx5-android/fcitx5-android/issues/402)）

同步的策略我设置的是每台设备在每天 11:00 的时候上传该设备的词库文件，上传完成后等待 5 分钟然后从 onedrive 上同步其他设备的文件，然后再通过命令使 rime 合并词库。

Linux 下触发 rime 同步有以下几种方式，来自 [issue](https://github.com/fcitx/fcitx5-rime/issues/54)
```
//qdbus
qdbus org.fcitx.Fcitx5 /controller org.fcitx.Fcitx.Controller1.SetConfig fcitx://config/addon/rime/deploy ''
qdbus org.fcitx.Fcitx5 /controller org.fcitx.Fcitx.Controller1.SetConfig fcitx://config/addon/rime/sync ''
//busctl
busctl call org.fcitx.Fcitx5 /controller org.fcitx.Fcitx.Controller1 SetConfig sv fcitx://config/addon/rime/sync s '' --user
busctl call org.fcitx.Fcitx5 /controller org.fcitx.Fcitx.Controller1 SetConfig sv fcitx://config/addon/rime/deploy s '' --user
//dbus-send
dbus-send --type=method_call --dest=org.fcitx.Fcitx5 /controller org.fcitx.Fcitx.Controller1.SetConfig string:fcitx://config/addon/rime/sync variant:string:''
dbus-send --type=method_call --dest=org.fcitx.Fcitx5 /controller org.fcitx.Fcitx.Controller1.SetConfig string:fcitx://config/addon/rime/deploy variant:string:''
```

Windows 的话只需要执行`C:\Program Files\Rime\weasel-0.16.1\WeaselDeployer.exe`，然后携带参数`/sync`即可

例如在终端执行，即可合并同步词库

```
"C:\Program Files\Rime\weasel-0.16.1\WeaselDeployer.exe" /sync
```

Android 的话，通过这个 [issue](https://github.com/fcitx5-android/fcitx5-android/issues/558) 可知 fcitx5-android 每半小时就会自动触发一次同步，因此不用使用任何命令执行。

### 自动化

为了方便自动化执行，在 Linux 和 Windows 下使用脚本来实现全自动化，Linux 使用 crontab 来设置定时任务，Windows 的话则是`任务计划程序`-> `创建任务`

sync_rime.sh

```
#!/bin/sh

# 定义路径和文件位置
LOCAL_SYNC_DIR="/home/Pan/.local/share/fcitx5/rime/sync/arch-linux/"
REMOTE_SYNC_DIR="rime:/rime/sync/arch-linux"
REMOTE_ROOT_DIR="rime:/rime/sync/"
LOCAL_ROOT_DIR="/home/Pan/.local/share/fcitx5/rime/sync/"

# 日志文件路径
LOG_FILE="/home/Pan/log/rclone_sync.log"

# 记录日志函数
log() {
    echo "$(date '+%Y-%m-%d %H:%M:%S')" $1 >> "$LOG_FILE"
}

log "==================== 同步任务开始 ===================="

# 同步本地到远程
log "正在同步本地到远程..."
rclone sync "$LOCAL_SYNC_DIR" "$REMOTE_SYNC_DIR"
EXIT_STATUS=$?

# 检查同步结果
if [ $EXIT_STATUS -eq 0 ]; then
    log "本地到远程同步成功"
else
    log "！！！本地到远程同步时发生未知错误：退出状态=$EXIT_STATUS"
fi

# 等待5分钟
log "等待5分钟..."
sleep 300

# 同步远程到本地
log "正在同步远程到本地..."
rclone sync "$REMOTE_ROOT_DIR" "$LOCAL_ROOT_DIR"
EXIT_STATUS=$?

# 检查同步结果
if [ $EXIT_STATUS -eq 0 ]; then
    log "远程到本地同步成功"
else
    log "！！！远程到本地同步时发生未知错误：退出状态=$EXIT_STATUS"
fi

# 合并词库
log "合并词库中..."
busctl call org.fcitx.Fcitx5 /controller org.fcitx.Fcitx.Controller1 SetConfig sv fcitx://config/addon/rime/sync s '' --user
EXIT_STATUS=$?

# 检查合并结果
if [ $EXIT_STATUS -eq 0 ]; then
    log "合并词库成功"
else
    log "！！！合并词库时发生未知错误：退出状态=$EXIT_STATUS"
fi

log "==================== 同步任务结束 ===================="
```

sync_rime.bat

```
@echo off
setlocal enabledelayedexpansion

REM 定义路径和文件位置
set LOCAL_SYNC_DIR=C:\Users\Pan\AppData\Roaming\Rime\sync\Surface
set REMOTE_SYNC_DIR=onedrive:/rime/sync/Surface
set LOCAL_ROOT_DIR=C:\Users\Pan\AppData\Roaming\Rime\sync
set REMOTE_ROOT_DIR=onedrive:/rime/sync
set LOG_FILE=C:\Users\Pan\Downloads\rclone_sync.log

REM 记录日志函数
set timestamp=%date% %time%
set logdate=%timestamp:~0,10% %timestamp:~-11,8%

call :logMessage "==================== 同步任务开始 ===================="

REM 同步本地到远程
call :logMessage "正在同步本地到远程..."
rclone sync %LOCAL_SYNC_DIR% %REMOTE_SYNC_DIR%
set EXIT_STATUS=%ERRORLEVEL%

REM 检查同步结果
if %EXIT_STATUS% equ 0 (
    call :logMessage "本地到远程同步成功"
) else (
    call :logMessage "本地到远程同步时发生未知错误：退出状态=%EXIT_STATUS%"
)

REM 等待5分钟
call :logMessage "等待5分钟..."
timeout /t 300

REM 同步远程到本地
call :logMessage "正在同步远程到本地..."
rclone sync %REMOTE_ROOT_DIR% %LOCAL_ROOT_DIR%
set EXIT_STATUS=%ERRORLEVEL%

REM 检查同步结果
if %EXIT_STATUS% equ 0 (
    call :logMessage "远程到本地同步成功"
) else (
    call :logMessage "远程到本地同步时发生未知错误：退出状态=%EXIT_STATUS%"
)

call :logMessage "合并中"
"C:\Program Files\Rime\weasel-0.16.1\WeaselDeployer.exe" /sync
set EXIT_STATUS=%ERRORLEVEL%

REM 检查合并结果
if %EXIT_STATUS% equ 0 (
    call :logMessage "合并成功"
) else (
    call :logMessage "合并发生未知错误：退出状态=%EXIT_STATUS%"
)


call :logMessage "==================== 同步任务结束 ===================="
endlocal
exit /b

:logMessage
echo [%logdate%] - %* >> %LOG_FILE%
exit /b
```

至于 Android 端的话，则是直接在 FolderSync 里设置定时任务执行

![FolderSync](https://img.panp.cc/2024/10/186f37d565b930e9550fd02da5d013f3.webp)


到目前为止一切运行良好🥳

当然这样的同步策略会把 sync 目录下其他文件一块同步下来，你可以添加个 `rm` 删除其他文件，我这边出于备份考虑就没有删去。

值得注意的是，在 Linux 和 Windows 下，雾凇拼音的配置文件和用户的配置文件是分开的，这时 rime 同步只会同步用户的文件，而不会将雾凇拼音的文件一块同步。

但是在 Android 上，雾凇拼音的安装目录和用户的配置文件只能位于同一处目录，因此会导致目录很杂乱，例如下面这个对比

![Android](https://img.panp.cc/2024/10/0b4b05560e08133f4407d2776bb985c5.webp)

![Linux](https://img.panp.cc/2024/10/a3ed396b7310587780575a3af8af2397.webp)

因此也可以选择只上传 txt 文件，这样远程目录会干净不少。

## 结束

配置 rime 其实对我来说也是一种“折腾”，以前使用过百度输入法和 Gboard，百度输入法的好处就是功能丰富，例如建议栏简单数学计算，云联想，丰富的皮肤等，其实还算好用，但是也能说的上是臃肿，即便是套上一层再好看的皮肤，也只是“金絮其外，败絮其内”。

而 Gboard 的话，我认为最好的点在于它可能是全网唯一支持双拼自动纠错的输入法了，但是我感觉对于双拼这类，本身简化了输入信息而降低了**容错率**，再配合自动纠错的话可能反而会适得其反，对我来说，似乎也没有那么大的吸引力。

而之所以开始使用 Rime，其实也是从接触 Linux 开始的，在使用 Linux 的时候了解到这个输入法。而在完成 Linux 端的配置后，当我再去使用 Windows 的时候，几乎完全不能忍受两端分裂感十分严重的输入体验，于是又给 Windows 配上了小狼毫。~~（然后再来一次，Android🤣）~~

尽管目前 Rime 在 Android 上的体验可能是最差的一端了，不过对我个人来说 Linux 和 Windows 用着还是相当舒适的。对于目前 Android 的 rime，似乎只有开源自由、隐私是为数不多值得使用的点了，在高度定制化上仍然还有很久的路要走......（也许你也可以考虑下[同文输入法](https://github.com/osfans/trime)）

如果你想要一个隐私，自由，顺手如意的输入法 也许 RIME 是一个不二之选，正如 RIME 官网所写

>聪明的输入法懂我心意

当然配置 RIME 的途中一定少不了各种麻烦，但在我看来这不也是一种乐趣吗🎉

>Just for fun.




