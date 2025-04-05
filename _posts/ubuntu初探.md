---
title: installing tools in ubuntu
date: 2025-02-01
tags:
  - 一生一芯
summary: 本文为本人通过查阅资料在mac虚拟机ubuntu上配置常用的工具，下载以后要用到的软件等流程。
---

# 常用命令

介绍在ubuntu的终端应用程序bash下的一些常用命令。

- poweroff：关机
- df -h: 查看磁盘使用情况
- echo $SHELL: 查看当前shell是什么
- lsb_release -a: 查看当前系统版本
- su -: 切换到root用户。初次使用需要先 su - passwd root激活root用户，之后就好了。也可以在命令前面加个sudo，输一次密码执行单次命令。
- adduser username sudo: 给用户添加sudo权限，将username替换成你自己的即可。

# 初始化流程

## 给apt换源

apt默认源是us的，速度很慢。需要换源。在ubuntu 24.04以后，源信息在/etc/apt/sources.list.d/ubuntu.sources里，可以sudo vim它来更改文件中的源信息。
这里注意，我本人使用MacBook pro m1，是arm架构的芯片。在清华源官网可以注意到arm架构需要使用ubuntu ports源，即x86和arm架构的源是不一样的，否则会出现failed to fetch 404 not found的错误。

## 更新

在计算机本地，系统会维护一个包列表，在这个列表里面，包含了软件信息以及软件包的依赖关系，在执行 apt install 命令时，会从这个列表中读取出想要安装的软件信息，包括下载地址、软件版本、依赖的包，同时 apt 会对依赖的包递归执行如上操作，直到不再有新的依赖包。如上得到的所有包，将会是在 apt install some-package 时安装的。
换完源后，执行sudo apt update和sudo apt-get upgrade来更新源信息。sudo apt update做的事情是：连接到远程的apt仓库，核对远程仓库版本和本地版本的差距，并且如果不一样对本地的一个记录软件包版本号的文件进行更新。但是不对软件包本身更新，只是记录其最新的版本是多少。而sudo apt-get upgrade是根据记录的最新版本号的版本号去更新软件包。2者结合使用即可。[apt和apt-get的区别以及update和upgrade的区别](https://linux.cn/article-14994-1.html)
用apt下载的命令为：apt-get install xxx

# 琐碎知识

## GNU gcc g++ gdb

[参考](https://www.sysgeek.cn/what-is-gnu/)

### GNU

有两个含义一个指的是编程用到的软件的工具包，包含了gcc g++ gdb等工具。另一个是指的是GNU的操作系统。我们现在说的Linux系统其实是GNU系统和Linux内核共同组成的。当时开发的GNU并没有内核，借用了Linux内核。

### gcc g++

通俗说gcc是c语言的编译器，g++是c++语言的编译器。

### gdb

在Linux系统下常用的调试工具。

## byobu

类似于oh my zsh和zsh的关系，byobu是ubuntu系统下terminal的一个开源替代软件。

## build essential

build-essential 是 Ubuntu 和其他基于 Debian 的 Linux 发行版中的一个元包，它包含了编译软件所必需的工具和库。这个包主要面向开发人员，尤其是那些需要从源代码编译软件的开发者。
包含：
GNU 调试器（gdb）：用于调试程序。
GNU 编译器集合（gcc, g++）：用于编译 C 和 C++ 程序。
make：用于自动化编译过程。
libc6-dev：GNU C 库，包含处理和编译 C 和 C++ 脚本所需的头文件和开发库。
dpkg-dev：用于解压、构建甚至上传 DEB 源包。
