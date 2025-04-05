---
title: MacOS+Vscode+Latex
date: 2025-03-25
summary: 本文记录了本人在macos系统上的vscode软件配置latex编写环境的过程。
tags:
  - projects
---

# 下载macOS下的latex编译器

macOS下有latex专门制作的编译器叫mactex，直接brew install --cask mactex下载即可
我在下载的时候遇到了sumcheck不一致的问题，有可能是使用镜像源未及时更新。于是直接在网站上下载。下载完后通过以下命令检测是否成功。
```
# check the version
pdftex --version
xelatex --version
# check the installation path
which pdftex    # /Library/TeX/texbin/pdftex
which xelatex   # /Library/TeX/texbin/xelatex
```
# 配置vscode的latex编辑环境
先下载latex workshop插件。之后在vscode的settings.json里添加对应的设置。
vscode的设置有2种形式，在手动选择的时候会同步更新到settings.json里面。以后移植的时候可以直接复制settings.json文件。
在下载之后，如果编辑latex文件可以看到在左边有对应选项。如果编辑不同类型的文件，可以选择不同的recipe也就是编译顺序和选项。
# 更多配置：单个pdf对应多个tex文件，拆分方法
使用subfiles宏包，可以看[overleaf的官方教程](https://www.overleaf.com/learn/latex/Multi-file_LaTeX_projects)。