---
title: 使用Xilinx vitis工具基于U50复现PipeCNN
date: 2025-02-01
tags:
  - projects
summary: 最后虽然没成功，但把学到的关于vitis的一些知识记下来。
---

# 有用的资料

https://docs.amd.com/v/u/en-US/ug1416-vitis-documentation
ug1416是vitis总的使用文档，里面包含了ug1700，ug1701等细一些的分支。

https://docs.amd.com/r/en-US/ug1700-vitis-accelerated-data-center/Getting-Started-with-Vitis
UG1700是最权威官方general文档，关于使用data center accelerated card进行加速。vitis的主要方向有data center accelerated card和embedded system进行加速。其中accelerated card指U50，U200等。较为全面的缺点就是较为复杂深入。

https://docs.amd.com/r/en-US/Vitis-Tutorials-Getting-Started/Vitis-Tutorials-Getting-Started-XD098
user- friendly guide关于如何使用vitis。

# preliminary vitis knowledge

## vitis是什么

vitis可以用于将C，C++和OpenCL代码转换为FPGA上的电路，进而进行异构计算heterogeneous computing和硬件加速。

## 使用vitis的三个build target

三个，分别是software emulation，hardware emulation和hardware。software emulation是用纯软件运行，用于检测bugs，syntax error等等。hardware simulation是将代码转换为RTL，放在host machine的一个模拟器里运行，也就是模拟硬件的运行。而hardware是先生成RTL代码，之后再生成xclbin文件。

## Xilinx Runtime and Platforms

如果进行数据中心加速卡的加速，另外需要安装3个软件包。

### XRT

第一个是runtime文件XRT。在vitis运行过程中，the software program is split into a host application that runs on the CPU and compute functions, or kernels that run on the Alveo data center accelerator card. The XRT runtime library provides an API enabling the host application to interact with the kernels on the accelerator cards. 其中在host machine上运行的应用使用gcc或g++来编译，同时在FPGA上运行的文件是使用v++来编译的。

### Vitis Platforms

这包括development target platform和deployment target platform文件。
其中development target platform是.deb文件，deployment target platform，在ubuntu 20.04，U50的情况下是.tar.gz文件。需要先解压，之后就生成了4个.deb文件，按照2022.1的文档按照顺序进行安装。如果sudo apt-get install安装不好就用dpkg -i安装。
之后仍然参考vitis 2020.1版本的文档。

其中一步是sudo add-apt-repository xxx，添加apt的源。但报错为add-apt-repository: command not found，这是因为add-apt-repository是作为software-properties-common这个较大的软件包的一部分包含的。需要先sudo apt install这个包。
一般add-apt- repository之后都是ppa: ...这里使用ppa的原因为：
如你所见，Ubuntu 对系统中的软件进行管理，更重要的是控制你在系统上获得哪个版本的软件。但想象一下开发人员发布了软件的新版本的情况。
Ubuntu 不会立即提供该新版本的软件。需要一个步骤来检查此新版本的软件是否与系统兼容，从而可以确保系统的稳定性。
但这也意味着它需要经过几周才能在 Ubuntu 上可用，在某些情况下，这可能需要几个月的时间。不是每个人都想等待那么长时间才能获得他们最喜欢的软件的新版本。
类似地，假设有人开发了一款软件，并希望 Ubuntu 将该软件包含在官方软件仓库中。在 Ubuntu 做出决定并将其包含在官方存软件仓库之前，还需要几个月的时间。
另一种情况是在 beta 测试阶段。即使官方软件仓库中提供了稳定版本的软件，软件开发人员也可能希望某些终端用户测试他们即将发布的版本。他们是如何使终端用户对即将发布的版本进行 beta 测试的呢？
通过 PPA！PPA是personal package archive的缩写。可以理解为开发者自己的一个源，发布软件使用。
使用完源后，可以通过add-apt-repository remove来移除对应的源。
之后的步骤其中，有一步是安装libgl1-mesa-glx这个包。但是显示无法定位，上网查询后得知这是一个暂时的包并且已经废弃。安装libgl1和libglx-mesa0可以达到相同的效果。

# 使用

以上都准备好之后，开始运行。首先需要2个source。

```
source <Vitis_install_path>/Vitis/2024.1/settings64.sh
#确定你要使用的vitis版本。
source /opt/xilinx/xrt/setup.sh
```

这两个source的作用涉及到在Linux系统下运行脚本文件的几个方法，以下做一些对比：
.sh文件是
其次，你使用的什么板子，需要下载对应的platform文件。这个过程叫installing data center card. 之后，To specify the location of any Data Center or Embedded platforms you have installed, set the following environment variable:

```
export PLATFORM_REPO_PATHS=<path to platforms>
```

# 结果
未能成功，因为pipeCNN需要2021版本的vitis，而xillinx官方已经不再发放旧版本的平台文件。使用新的平台文件会出现bug。同时使用新版本的vitis会显示找不到v++，可能是版本的原因，因为2021版本的vitis确实在bin文件夹里有v++这个文件。