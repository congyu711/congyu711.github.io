---
layout: post
title:  "github page"
date:   2021-11-22 11:30:08 +0800
categories: github page
---

## 配置github page

[docs.github.com](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages)

## 使用mathjax

[https://jekyllrb.com/docs/themes/#overriding-theme-defaults](https://jekyllrb.com/docs/themes/#overriding-theme-defaults)
默认不显示```_layout, _includes ```等文件夹，按照上面的文章找到对应theme的```_layout, _includes ```位置，复制到仓库根目录下，在```_layout\post.html```中的header中加入
```html
    <script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
    <script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
        <script> 
        MathJax = {
          tex: {
            inlineMath: [['$', '$']],
            processEscapes: true
          }
        };
        </script>
```
即可使用LaTeX

我觉得这样就能用了。


![](https://github.com/congyu711/congyu711.github.io/blob/master/docs/image/%D0%93%D0%BE%D1%80%D0%B0_%D0%93%D0%BE%D0%B2%D0%B5%D1%80%D0%BB%D0%B0_%D0%BF%D1%96%D1%81%D0%BB%D1%8F_%D0%B7%D0%B0%D1%85%D0%BE%D0%B4%D1%83_%D1%81%D0%BE%D0%BD%D1%86%D1%8F.jpg)