---
title: exploring tools in ubuntu
date: 2025-02-03
tags:
  - 一生一芯
summary: 本文为本人通过查阅资料在mac虚拟机ubuntu上使用基础的Linux命令，并且使用vim，gcc，gdb等工具等学习记录
---

# 基础命令和工具

Linux下的常用命令包括find，grep，du，more，less等。还会涉及到[正则表达式](https://linux.vbird.org/linux_basic/centos7/0330regularex.php)的相关知识。另外几个常用的重定向符号为|,<,>。
如果在Linux系统下编写程序，需要用到vim，gcc，gdb，Makefile等。
先用vim编写，之后用
gcc -o hello.c hello
意思是gcc编译hello.c文件，生成可执行文件hello。（也可以用Makefile编写文件之间的dependencies逻辑，自动化运行）运行只需要./hello即可。
上面的命令产生的是程序的release版本。程序还有debug版本。如果用gdb来调试必须对debug版本进行操作。生成debug版本的方法就是加上-g flag，即gcc -g xxx，否则程序运行中出现的中间信息不会被添加到文件中，也就无法被gdb用来分析。

# gdb

gdb会记忆你的命令，如果和上次命令相同直接回车即可。

## 常用命令

- l 1/l 0：显示前10行的code，可以按enter接着显示下10行。
- b 行号：在某行添加断点。更全面的表达是b 文件名：函数名/行号，在对应源文件的某函数第一行或者某一行添加断点。
- info b：查看所有断点信息。info会查看所有调试信息。
- d 断点号：删除断点。断点号可以在info b中看出。d breakpoints是删除所有断点。
- disable b：暂时禁用所有断点。对称的是enable b。可以在b后加上具体断点的编号，禁用某个断点。
- r：运行程序。无断点就运行完。有断点运行到第一个断点前。
- n：单步执行，next。不会进入函数内部。
- s：逐语句执行，step。如果是函数调用，会进入函数内部。
- p：print，后面跟变量名。
- display：和p的格式类似，只不过是持续跟踪。在每次n或s的时候都显示变量的具体值。
- undisplay 变量编号：取消跟踪。注意变量和断点一样也有编号，对编号操作不是对变量名操作。
- set var 变量名=变量值：修改变量的值。类似于条件断点，直接跳转到对应的条件处。
- until 行号：跳转到某行。类似于上面的set var，是一种指定跳转到某个地方的命令。
- finish：当在一个函数内部的时候，输入finish直接就执行完这个函数并跳转到调用这个函数的地方。
- c：continue。继续运行程序到下一个断点处。可见在gdb里下一步有三个不同的颗粒度：n s c。运行到下一个语句（不进入函数和进入函数）以及运行到下一个断点。

# tmux 终端复用工具

tmux 是一个非常实用的终端复用工具，它允许在单个终端窗口中运行多个终端会话。即使 SSH 连接断开，tmux 会话中的程序仍然能继续运行。
关于tmux更多内容可以参考[这篇blog](https://louiszhai.github.io/2017/09/30/tmux/)

## tmux 基本概念

- Session（会话）：一个 tmux 运行实例，包含一个或多个 Window
- Window（窗口）：一个全屏工作区，包含一个或多个 Pane
- Pane（面板）：一个分割的终端区域

他们三个的关系是：一个回话可以有多个窗口，一个窗口可以有多个面板。[这里我创建了三个回话 每个回话多个窗口，有的窗口多个面板](https://ooo.0x0.ooo/2025/02/05/OGEFOI.png)。不同会话可以运行不同的程序，并且回话和当前的window可以拆分。如在创建新回话后，进去运行了某个程序，之后可以退出当前会话，这个时候程序还在后台运行，下次想加入可以直接tmux attach -t session-name即可。不同的窗口window可以运行不同的terminal language，比如bash和zsh等等。

使用c b+w可以查看窗口列表，如上文图片所示。

## 常用命令

- 创建新会话：`tmux new -s session-name`
- 查看所有会话：`tmux ls`
- 接入会话：`tmux attach -t session-name`
- 退出当前会话：`Ctrl+b d`（先按 Ctrl+b，再按 d）

### 窗口操作（按 Ctrl+b 后）

- c：创建新窗口
- n：切换到下一个窗口
- p：切换到上一个窗口
- &：关闭当前窗口
- 数字键：切换到对应编号的窗口

### 面板操作（按 Ctrl+b 后）

- %：垂直分割面板
- "：水平分割面板
- 方向键：在面板之间移动
- x：关闭当前面板
- z：最大化/还原当前面板
