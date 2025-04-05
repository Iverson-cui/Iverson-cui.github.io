---
title: FPGA串口通信实验
tags:
  - projects
date: 2025-02-01
summary: 本文为使用basys3FPGA并且调用串口实现和PC手机蓝牙通信的全过程。
---

# vivado项目设置

- 对于basys3板子，型号为xc7a35tcpg236-1。
- 在I/O planning的时候，I/O std指的就是不同板子的电平标准。对于basys3来说，电平标准是LVCMO33。不同的电平标准会使得接口的1/0对应的电压表示不同。
- 不同输入输出变量package pin选择板子上对应引脚的号。
- 如果你要跑一个simulation，则这个project至少需要2部分verilog文件。第一部分是项目本身的代码，里面是这个项目需要用到的module，第二部分是testbench，里面是对时间刻度，参数变化的设置和module的实例化。一个是design source一个是simulation source。这是因为你只是仿真模拟一下，而不是上板通过板子的管脚来控制信号的变化。如果上板子做implementation的话，你的输入输出信号都需要分配到板子的端口型号上，这样输入输出都可以通过板子来控制或监控。这个时候simulation source就是不必要的。
- 在implementation的时候，vivado会选择一个top level的文件里面的一个top module作为主要的module，project的输入输出就是这个module的输入输出。所以这就涉及到top level file的选择和top module的选择。一般情况下top level file里只有一个module的definition。你可以像引用函数一样，写2个verilog file，一个是sub- module，另外一个top-module里面有sub- module的instantiation。之后右键选择这个文件后点击set as top将其设置为top level file。你也可以在top level file里创建多个module，但注意top level file里没有instantiation。
- vivado软件能打开的文件格式是.xpr文件，在打开xpr文件后会生成project。但是代码是.v文件。他们不一定在一个路径下。

# 工具的使用

## 在线逻辑分析仪

FPGA项目开发包括behavioral simulation，是仿真波形图。如果为了进一步观察上板后的信号变化，可以使用在线逻辑分析仪，它可以监控FPGA里面的某个变量的值，将其波形图返回到PC上。vivado的叫ILA，quartus的叫signaltap。纯FPGA项目推荐使用实例化IP核的方法来调用ILA。具体流程为在IP catelog里面搜索ILA，设置探针数目和sample data depth。设置完后会生成一个IP内核文件，但是这个文件和top file目前是同级文件。需要进一步将其例化到top file里面。在旁边的IP sources的instantiation template里面可以找到例化的模板。
还可以用mark debug的方法。在想添加probe的信号前面添加(_ MARK_DEBUG="true" _)。之后先synthesis，open synthesized design，点击debug就能在unassigned debug nets里看到我们的那几个信号。右键后选择create debug core，选择名字和信号深度等等。之后，通过create debug port可以添加探针端，通过assign to debug port将信号和探针绑定。

## XDC文件的编写

在implementation的时候，需要将top level file里面提到的所有输入输出都有对应的管脚号。这个对应的信息就存储在一个.xdc文件里。你可以手动编写这个文件来更改这个对应，也可以通过I/O planning的GUI来手动更改。更合适的方法是寻找对应开发板的general XDC又叫master XDC文件，然后对于每个项目做出具体的comment out等等。
比如这是一行XDC文件里的代码：

```
# set_property -dict { PACKAGE_PIN G1  IOSTANDARD LVCMOS33 } [get_ports { led[0] }];
```

G1代表对应的管脚号，可以从FPGA的manual里查到。比如basys3的时钟管脚号是W5，UART的RX管脚号是A18（需要连接PC的TX端口）。之后把get_ports后面的内容改为变量名。（电平标准也要确保正确）
比如对于clk信号的更改就是：

```
set_property -dict { PACKAGE_PIN W5  IOSTANDARD LVCMOS33 } [get_ports clk];
```

## basys3开发板

根据basys3的manual，项目的bitstream的一种写入方案是可以通过其USB- JTAG接口烧入。（蓝色胶皮套mode jumper的位置决定了使用哪种方法烧写）JTAG是一种接口规范，定义了四个信号，可以通过这四个信号来控制FPGA的内部信号，在这里可以用来将bitstream文件写入FPGA让其配置内部的资源和电路。正常的FPGA需要下载器，basys3不需要，因为其已经集成了JTAG芯片在内部，可以实现USB到JTAG的转换，只需要用USB下载即可。当然也可以选择使用digilent的下载器来从另外一个端口进行烧写。
同时，也集成了USB转UART的芯片，型号是FTDI公司的FT2232H，所以使用一个USB就可以完成供电，程序的烧写和串口数据的传输。注意从PC接收数据的时候对应的管脚为B18
basys3可以选择USB供电或者单独的外接电源供电。但必须保证电压都在5V左右。蓝色的胶皮套的位置是用来选择哪种方案供电的。
basys3自带内存模块memory，但是本项目中无需使用。
Basys3还自带了许多Pmod ports。Pmod意思是peripheral module，全是母头，可以驱动外部设备，比如蓝牙等。有的母头管脚在工作的时候是VCC的TTL电平和GND的电平。

## HC-05蓝牙模块

HC-05是蓝牙模块，可以通过USB接口连接到PC，通过串口调试助手来控制HC-05。HC-05的引脚是TTL电平，所以需要一个转换芯片。
与此同时，查找HC-05的manual可得其必须由5v电压驱动，但是basys3的Pmod口驱动电压是3.3v，所以需要电平转换模块或者DC- DC升压降压模块。
电平转换模块必须输入3.3v和5v的电压。而升压降压模块可以直接转换。
蓝牙模块有普通模式和AT模式，前者可以沟通，后者是用来调节参数的。
HC-05有两个模式，AT模式也就是命令响应模式；工作模式。工作模式分为主机，从机和回环模式。
与蓝牙模块的通信一般都是调用串口，即蓝牙插在电脑上，并且另一个设备和蓝牙实现无线连接。这样2个设备都调用串口进行通信，蓝牙本质上就是串口。
和串口通信可以采用串口调试助手或蓝牙调试助手，也可以使用python脚本。调用pyserial库。还可以调用time库来计算时延。
值得注意的是，ser.in_waiting是接收端的buffer。计时是先清空buffer，之后一旦检测到buffer有东西，就说明开始传输了数据。一直到超过一个时间后buffer里仍然没有数据变化，就说明数据传输完毕。

## USB转TTL

TTL口就是公脚。电脑的输出电压通过USB或者DB9接口，遵循RS232协议或USB协议。但边缘设备等等都是TTL电压驱动，所以需要这个模块进行转化。有的USB转TTL芯片在TTL的输出口可以选择输出电压是3.3V还是5V，这样就可以让他来驱动蓝牙模块。
注意USB转TTL的接口和HC-05蓝牙接口直接的引脚对应。一个的输入对应另一个的输出，交叉。

# 项目流程

背景是通过调用UART串口，实现FPGA和PC端之间的数据通信。特别的，PC向FPGA发送数据，数据存储在FPGA的reg变量里面。

## 所需

- 一个basys3板子
- PC
- PC上要有串口调试助手，本人使用Xcom和SSCom

## 代码框架

https://ooo.0x0.ooo/2025/02/24/OQW3bK.png
