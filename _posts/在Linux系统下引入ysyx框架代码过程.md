---
title: 在Linux下引入ysyx代码过程
tags:
  - 一生一芯
date: 2025-02-01
---

全过程包括在Linux系统上下载代理软件，重新生成一个新的SSH key并添加到自己的账户上。

# 复制粘贴

为了实现在虚拟机和实机上能复制粘贴文字，我查询了相关资料。得知需要安装vmware-tools。但我的选项卡里根本没有这一行，更不用说能否安装。后来输入命令：
sudo apt-get autoremove open-mv-tools
将已经安装好的tools删掉。之后重新手动安装。
sudo apt-get install open-mv-tools
sudo apt-get install ppen-mv-tools-desktop
都安装完后，别忘了重启一下虚拟机
sudo reboot
之后就可以发现正常使用。

# 下载代理

Linux系统得到的安装包有可能是.deb文件，在./Downloads/目录下。安装deb文件的方法是：
sudo apt install ./filename.deb
另外有一种安装包是Appimage文件，更加便携方便，移植性强。
在下载代理文件的时候，犯的错误是：虚拟机本身处于24.04版本，新版本情况下很多软件本身不会及时的做适配，导致如果使用旧的文件会出现依赖的问题。同时下载clash的时候没有选择官方的途径而是从vpn网站提供的网址上下载的.deb文件。这样得到的文件往往因为不及时维护而版本过旧出现依赖问题。

# SSH key

使用SSH和GitHub仓库建立连接的步骤为：

1. 生成SSH key
2. 将这个key加入到SSH agent
3. 将SSH key加入到对应的GitHub account上。
   在做完以上三个步骤后就可以git clone，否则会报错。

# clone代码

在输入命令后，成功clone了对应的repository。接下来要做的是git config。这是一个类似于设置profile的命令。分为3个granularity：全局，即当前电脑上所有user；某一个user的所有repository；某一个单独的repository。
你的个性化设置其实存储在名为gitconfig的一个文件里面。不同的层级对应不同的文件，但名字都是这个。其实你通过命令行改变的设置本质就是改变了文件里的内容。
常用的需要调节的有user.name，user.email。如果system level就加--system，如果user level就加--global，如果local level就加-- local或者移动到对应的repository里面直接执行不加flag即可。

# git使用

git basics网站
[这个网站](https://onlywei.github.io/explain-git-with-d3/)描述了git常用命令的visualization版本。

# compiling and running NEMU

在ysyx项目中，NEMU指的是[NJU EMulator](https://github.com/NJU-ProjectN/nemu)，是ysyx里面的CPU模拟器。
NEMU也常被用在Linux内核的配置和嵌入式领域。

## prerequisite

libncurses-dev,lib-readline-dev,flex,bison这四个是原始框架里面没有的，需要用apt-get来安装。

## make menuconfig

当在Linux内核里运行make menuconfig之后，会出现一个文本的user interface，可以在里面调整对应的配置。
没有调整直接退出。以上的步骤会产生一个.config文件，作为配置的保存。

## make

在有了.config的情况下可以用make命令来进行编译。编译的时候会参考.config里面的设置。

## make run

在编译完之后可以用make run来运行模拟器。

## git log

git log可以用来记录commit的历史。在ysyx项目中q助教会通过git log来判断你的任务完成情况。所以git log常用于记录任务过去完成的情况。
ysyx的这个项目里有自动跟踪的系统，会将命令自动更新commit。也可以manual commit，就是手动git add .和git commit --allow-empty。加上--allow-empty的flag可以允许我们在不做出任何更改的情况下进行commit。git log --author="xx"可以搜索某个author的commit。这里的xx可以是一部分，命令会显示出所有author里包含这些字符的commit。
