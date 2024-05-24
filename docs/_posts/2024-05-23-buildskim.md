---
layout: post
author: congyu
title:  "customize skim pdf reader"
date:   2024-05-23 00:00:00 +0800
categories: macos
---

skim 长这个样子(不是那个[fuzzy finder in rust](https://github.com/lotabout/skim))

![skim]({{url}}/assets/image/skim/skim.png)

和preview一样用apple pdfkit, 但是很多地方做的要更好, 而且还在[sourceforge](https://sourceforge.net/p/skim-app/code/HEAD/tree/)上开源的

[据说](https://www.reddit.com/r/vim/comments/3prfd0/pdf_reader_with_vim_keybindings_for_mac_osx/) [ZATHURA](https://pwmt.org/projects/zathura/)差不多是个能读pdf的vim, 但是对mac的屏幕支持有点垃圾. 没有用过

之前最好用的pdf reader是okular, 可以很方便的用一些 vim keybindings, 但是skim不行, 所以要试试改源码了

另外我在用 karabiner, 有些很好用的配制比如 [https://ke-complex-modifications.pqrs.org/?q=vim](https://ke-complex-modifications.pqrs.org/?q=vim), 在normal mode下 `j` `k` 貌似直接映射到 上下箭头. 

![karabiner-vim-plus]({{url}}/assets/image/skim/karabiner.png)

原本使用 karabiner 就可以解决问题了, 但是skim没有自己处理按上下箭头之后的操作而是扔给pdfkit里面的代码处理. apple认为按`DownArrow` 你只需要将屏幕下移1/3行, 移动太慢完全无法使用.

![SKPDFView-keydown]({{url}}/assets/image/skim/skpdfview.png)

大概是1848行那里直接扔给另外的 `keyDown` 来处理了.

不过好在 objective c 足够像 C++, 即使从来没接触过, 当成大概也能改一改.

需要去 [sourceforge](https://sourceforge.net/p/skim-app/code/HEAD/tree/) 那里搞到源码, 扔到xcode里面按照网站上说的办法就能编译了.

需要改的是 `SKPDFView.m`, 处理键盘按键的函数在1746行叫做 `- (void)keyDown:(NSEvent *)theEvent`

只要在里面加上处理对应 `eventChar` 的 else if 就好了.

![code]({{url}}/assets/image/skim/vim.png)

然后需要实现 `myscrollDown`,`myscrollUp`. 在别的代码里可以翻到skim是怎么写scroll操作的, 直接抄过来.

在 `SKMainWindowController_Actions.m` 里面有这样的代码

```objective-c
- (void)scrollUp:(id)sender {
    NSScrollView *scrollView = [[self pdfView] scrollView];
    NSClipView *clipView = [scrollView contentView];
    NSPoint point = [clipView bounds].origin;
    point.y += [clipView isFlipped] ? -4.0 * [scrollView verticalLineScroll] 
    : 4.0 * [scrollViewverticalLineScroll];
    [clipView scrollPoint:point];
}

- (void)scrollDown:(id)sender {
    NSScrollView *scrollView = [[self pdfView] scrollView];
    NSClipView *clipView = [scrollView contentView];
    NSPoint point = [clipView bounds].origin;
    point.y += [clipView isFlipped] ? 4.0 * [scrollView verticalLineScroll] 
    : -4.0 * [scrollViewverticalLineScroll];
    [clipView scrollPoint:point];
}
```

抄过来然后根据xcode的提示修改一下

![code]({{url}}/assets/image/skim/myscroll.png)

编译, 找到skim.app, 扔到application里面, 结束!

完全不会写objective c也能加点功能 :)