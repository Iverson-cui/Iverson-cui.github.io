---
title: verilator and NVboard
tags:
  - 一生一芯
date: 2025-04-11
summary: 本文为本人完成NJU lab6的记录
---
# background
因为NJU lab6涉及到了键盘的使用，较为复杂。如果使用FPGA需要自己定义声明端口。但是使用nvboard端口声明等容易搞乱，特此写这篇文章理清一下思路。
# 调用verilog模块
所有的v代码，包括keyboard.v和top.v都只是描述了电路，模块，输入端口。真正让电路跑起来需要给输入端口数据。
1. 真正上板的情况下，板子上有芯片管脚，对应的芯片提供数据；板子上的外接管脚是你自己连接提供数据。
2. 写testbench做simulation的情况下，是你自己的testbench里声明一堆wire和reg，让他们分别和module的input和output相连。之后给出作为input的wire和reg的值。
3. 在nvboard和verilator结合的情况下，有2个方法。一种是在nxdc文件里写好某个module的input output和哪些端口连接，之后可以向端口输出数据或者从端口拿数据。第二种是在verilator要求生成的cpp wrapper文件里，使用->符号来给输入输出信号赋值。
# 对于nvboard的example
在nvboard的example里，keyboard模块的端口声明是：clk,resetn,ps2_clk,ps2_data，这些全是输入。ps2_clk和ps2_data是键盘模块的时钟端口和键盘模块获得输入的端口。clk是外部端口。没有输出端口，是因为在example里做的事情就是反复接收输入之后在terminal打印出来，并不对键盘的数据做进一步处理。
继续看nxdc文件，发现键盘的ps2_clk和ps2_data端口连接在了PS2_CLK和PS2_DAT端口上。在wrapper cpp文件里，只有nvboard_update.所以可得，nvboard的底层设计逻辑使得PS2_CLK作为一个管脚，一直在输出时钟，PS2_DAT会自动捕获你打字的键盘信息并以串行的方式和PS2_CLK配合输入信号。
# 对于lab6给出的实例代码
同样的键盘代码，输入输出端口变为：
clk,clrn,ps2_clk,ps2_data,data,ready,nextdata_n,overflow
其中[7:0] data overflow ready是输出端口，这回可以自行设计另外一个数码管模块等等将键盘的扫描码从data取出进行利用。

下文还给出了testbench代码。testbench分为2个文件，有2个模块。因为键盘模块要求ps2_data和ps2_clk互相配合，通过11个周期串行发送数据，为了方便起见，就有了第1个模块，目的就是接收一个8 bits，转换为串行的数据。并给出时钟信号。

如果想用nvboard和verilator的话，只需要将大的键盘模块的ps2_clk和ps2_data端口连接在了PS2_CLK和PS2_DAT端口上，这样敲击键盘，数据就会从PS2_DAT端口进入模块，并且从ps2_data出来的数据就可以传入数码管模块等等。

如果是真实上板，首先需要有个时钟生成模块，生成系统clk和键盘clk，之后连接一个键盘的话，得确保键盘走的是ps2协议，发送的数据是串行的，还要确保板子上的键盘clk和实体键盘真正用的clk是一致的。