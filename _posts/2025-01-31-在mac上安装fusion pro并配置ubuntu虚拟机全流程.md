---
title: using ubunto virtual machine in mac
tags:
  - 一生一芯
date: 2025-01-31
summary: 本文为本人通过查阅资料在mac上下载fusion pro并配置ubuntu虚拟机的过程。
---

# 下载fusion pro

通过查阅资料去官网下载fusion pro。注意官网是broadcam的官网，下载fusion pro需要先登录broadcam的账号。

# 寻找iso源文件

可以直接去ubuntu官网下载iso源文件，也可以去国内镜像替代源下载。

# 安装

将iso文件拖拽进fusion pro中按照步骤安装。
需要注意的是，

1. 安装过程中有一处选择alternate sources，需要选择mirror，可以自行寻找国内镜像源网址，否则后续会出现超时。
2. 我在安装完之后是server版，也就是只有command line，没有desktop，需要用apt-get自行下载desktop版。
   这里补充一点，Linux distribution的各个系统里，命令行和桌面端是分开的。桌面也是个单独的应用程序，和Linux内核是分开开发的。常用的桌面端程序有：KDE plasma，GNOME，xXfce等。这里下载的ubuntu desktop也是一个应用程序。

# 补充知识

## snaps

在安装的时候会有一处涉及到snap，查阅资料可得，在ubuntu中snap的意思是：A snap bundles an application and all its dependents into one compressed file. The dependents might be library files, web or database servers, or anything else an application must have to launch and run. 可以简单理解为一个应用程序。比如Firefox snap就是Firefox应用程序。
