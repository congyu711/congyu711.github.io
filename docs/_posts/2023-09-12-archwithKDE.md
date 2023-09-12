---
layout: post
title:  "A Man Used Arch Linux For 3 Months. This Is What Happened To His Mind."
date:   2023-09-12 0:00:00 +0800
categories: random_thoughts
---

从三个月前开始用 Arch+KDE, 感觉现在感觉非常烦躁.

linux的桌面环境完全就是一团狗屎. 永远不可能自己修好所有问题, 让你狠狠体会什么叫做compromise.

在笔记本上用, 硬件是 xps9500, 10750h带一个intel的显卡, 还有一个1650Ti(linux下从来没用过). 笔记本屏幕是1920x1200的, 另外lab有一个4k的屏幕.

### 我需要做什么?

1. chrome. 很多东西在谷歌的账号里, 所以要用chrome而不是chromium, 开源版本无法登陆账号
2. okular. kde自带的okular是我最喜欢的pdf阅读器
3. vscode. 写点代码
4. 一个能方便录制窗口的录屏软件
5. 好用的terminal, 比如 konsole 就不错
6. 输入法, 要写点中文的啊
7. ...

遇到好多问题, 所有问题基本都要把 x11和wayland分开说

### touchpad gesture

system level的手势操作识别 x11 是根本没有的, 不过touchegg这个软件提供不错的自定义.
wayland默认的操作很没用, 看论坛改起来有点麻烦, 没有改过.

chrome 就有超级多的问题. 
横向滚动在历史页面切换这个功能在windows和mac都没什么问题, x11 下加个flag也能工作(仅限libinput), wayland下如果用的是xwayland就直接失效, 没有任何办法, 原生wayland(在chrome那里改ozone platform)可以正常工作(后面就会发现用原生wayland chrome 仍然无法解决的输入法问题). 

touchpad pinch zoom 在x11上是不太行的, firefox有[一些办法](https://superuser.com/questions/1670434/firefox-touchpad-pinch-to-zoom-not-working-but-touch-pinch-to-zoom-works-on-linu), 但是chrome就完全不行了. chrome如果把ozplatform改成wayland的话可以用.

### 输入法

chrome 支持输入法的情况可以看 [https://www.csslayer.info/wordpress/fcitx-dev/chrome-state-of-input-method-on-wayland/](https://www.csslayer.info/wordpress/fcitx-dev/chrome-state-of-input-method-on-wayland/)

x11 chrome使用gtk3, 输入法完全正常. wayland下chrome只有改用gtk4才能正常使用输入法. 然而用gtk4会让kde的global menu获取不到菜单(不过也不是大问题吧)

### 多显示器

x11 并没有什么方便的办法给两个显示器设置不同的缩放比例. 

wayland可以但是会出现奇怪的问题, 窗口最大化之后缩小卡顿, 最大化的xwayland窗口拖动标题栏会直接移动等等, 用起来很奇怪.

感觉目前还没到一个可用的状态
