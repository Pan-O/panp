+++
title = 'Gnome简单美化指北'
date = 2024-05-31T08:00:00+08:00
toc = true
tags = ["Linux", "美化", "桌面"]
+++

## 起始
在前两周装了台ITX后便马上装上了馋了很久的arch linux，在不同的DE中选了GNOME，相比于kde来说，我还是更喜欢gnome的简洁，但是gnome默认也不太好看，也缺少一些功能，这里就记录一下个人配置。

注：本文不包括终端 shell的美化，配置。本文内容皆基于本人个人理解，如果有误，欢迎指出。

## 效果一览
![style](/post-img/gnome-style/1717161651922.png)
![style](/post-img/gnome-style/1717161652968.png)
![style](/post-img/gnome-style/1717161642956.png)
![style](/post-img/gnome-style/1717161646018.png)

## 前置要求
Gnome 46
已安装aur包管理器，`yay` `paru`均可，paru会更新一些

安装拓展可以使用浏览器拓展或者命令行安装，浏览器安装需要提前安装[Gnome Shell 集成](https://chromewebstore.google.com/detail/gnome-shell-%E9%9B%86%E6%88%90/gphhapmejobijbbhgpjhcjognlahblep)

## Theme
目前只用了shell主题和gtk主题，至于qt主题因为还没找到喜欢的，也没有特别影响整体效果就没有配置。
### Shell主题
Shell主题主要使用的是[Marble Shell Theme](https://github.com/imarkoff/Marble-shell-theme)
基本的安装方法在README.md里已经相当详细了

同时Marble还有一个GDM theme，有简单的美化，也可以一起安装了
### GTK主题
gtk主题用的是[Catppuccin](https://github.com/catppuccin/gtk)的Macchiato版（强推Catppuccin，对于很多应用或者网页都要相应的配色方案，可以做到色彩统一

## Extension
### 系统拓展
Removable Drive Menu 在顶部bar添加移动设备的管理

User Themes 支持用户使用指定的Shell主题

### 用户拓展
[AppIndicator and KStatusNotifierItem Support ](https://extensions.gnome.org/extension/615/appindicator-support/) 在顶栏中显示程序的托盘图标

[App menu is back](https://extensions.gnome.org/extension/6433/app-menu-is-back/) 在顶栏中显示当前使用的应用（好像是前几个版本被删掉的特性，个人感觉还算蛮有必要的，用这个可以加回来

[Blur my Shell](https://extensions.gnome.org/extension/3193/blur-my-shell/) 强推，能够模糊各个部位的shell样式，对于顶栏我配置了全屏时不模糊（启用Disable in overview），非全屏时直接Sigma和Brightness为0，这样可以做到非全状态下顶栏透明，全屏下有背景色，有效避免全屏后顶栏样式的突兀

[Clipboard Indicator](https://extensions.gnome.org/extension/779/clipboard-indicator/) 在顶栏中添加一个剪贴板按钮，不算特别好用，不过算一个比较简单的剪贴板解决方案，支持文本和图片

[Dash to Dock](https://extensions.gnome.org/extension/307/dash-to-dock/) 将概览时的dash栏变为dock栏，配置里我只修改了`点击动作`为`最小化或显示预览` ~~让体验更像windows了~~

[Executor](https://extensions.gnome.org/extension/2932/executor/) 在shell里添加shell脚本，我这里主要用来显示歌词，使用了[Get_Lrc](https://github.com/MarsSwimmer/get_lrc)这个项目，不过如果不播放歌的话，这个脚本是不能获得到歌词的，但是还是会在顶栏占用空间，解决办法估计只能专门写个单独的拓展才行了

[Focus changer](https://extensions.gnome.org/extension/4627/focus-changer/) 通过快捷键切换焦点到其他应用，感觉效果还行

[GNOME Fuzzy App Search](https://extensions.gnome.org/extension/3956/gnome-fuzzy-app-search/) 让gnome的搜索应用方面更加“智能”，便于快速检索应用

[Input Method Panel](https://extensions.gnome.org/extension/261/kimpanel/) 修改Fcitx5的样式，这样Fcitx5不用配置什么皮肤也挺好看了

[Just Perfection](https://extensions.gnome.org/extension/3843/just-perfection/) 对一些Shell组件修改，例如隐藏一些不需要的部件。我只用到了隐藏`辅助功能菜单`，之所以有这个是因为设置里我的字体dpi调到了1.1（由于启用分数缩放导致不少应用有模糊的问题，只能这样曲线救国了

[User Avatar In quick Settings](https://extensions.gnome.org/extension/5506/user-avatar-in-quick-settings/) 在快速设置菜单添加你的头像和名称等信息（感觉加了似乎好看点？？

[Vitals](https://extensions.gnome.org/extension/1460/vitals/) 在顶栏添加对一些系统信息的展示，例如内存使用占比，网速等。比自带的System Monitor好用

## 结语
初步配置完仍然有些不太满意，例如gtk主题应用并不完全，圆角，模糊等插件的失效，以及传言中gnome大更新时插件会大量失效都让人有点不安（之所以传言是因为我还没体验过），最后最主要是gnome是堆叠式的，这点与Windows和Mac并没有太大区别。
因此在初步配置好后其实与Windows和Mac在基础操作上基本大差不差。

本着折腾和“个性化”的想法，接下来估计会开始折腾hyprland了，体验一下平铺式桌面管理的快乐。至于Wayland的兼容性方面...等用上了再说🤣，相信一定能解决的办法。
在GNOME下默认也是使用Wayland，基本没感觉有什么问题，分数缩放方面确实不好处理，据说kde有比较好的解决方案，不过还没用过kde🥲
