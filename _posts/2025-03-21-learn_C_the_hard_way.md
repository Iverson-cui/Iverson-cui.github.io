---
title: learn C the hard way assignment doc
tags:
  - 一生一芯
date: 2025-03-21
summary: 按照ysyx的标准，本文记录了作业完成的情况。
---

# ex1

打印了带有\n的换行输出。
查阅了puts和fputs的文档，记录下了相应的区别和用法。

# ex2

all:ex1将all作为make的默认target，之后导到ex1进行编译。
如果cc带上g就是带上debug信息，方便gdb使用。-Wall就是调整警告的等级，显示所有警告。

# ex3

查看man fprintf，记录下了里面列举的conversion specifier
make file已做出更改。

# ex4

下载了valgrind，并用在了test.c上。
valgrind源码的makefile十分复杂，包含了不同环境情况下不同的编译命令。

# ex5

每句话都能理解

# ex6

查阅了输出数字对应的占位符的不同情况，包括不同进制的情况。
打印空字符串直接%s 后面是""即可

# ex7

当long过长的时候，报错integer literal is too large to be represented in any integer type
long能表示2^{63}-1最大，unsigned是他的两倍。
char其实被当作对于ASCII的int来处理，char和int就等于char的ASCII和int相乘。
查阅资料可得，int的大小和CPU的位数没有必然联系。在64位系统下，只能说明指针是64 bits，但是不能说明int的大小。int的长度是编译器决定的，一般情况下不管32还是64位都是4 bytes。

# ex9

会报错 incompatible pointer to integer conversion assigning to 'int' from 'char[4]' [-Wint-conversion]
如果将names的4个字符全赋值成数字 会报错Syscall param write(buf) points to uninitialised byte(s)，原因是不以\0结尾。如果只赋值前三个，便不会报错。
如果一个字符数组占四个字节，一个整数也占4个字节，则我的方法是通过移位<<将每个char转化为int后组合成一个大int来使用。
只需要把name赋给一个char \*即可

# ex10

for里有三个部分，初始化 条件 更新。三个部分都可以用逗号分隔多个语句。初始化和更新部分用逗号分隔的所有语句都会执行，但是条件部分只会执行最后一个，类似于逗号运算符。
null是空地址的意思。用null给字符指针数组的一个元素赋值会使这个指针指向空地址。最后输出：state 4: (null)
可以，直接让state[num]=argv[num]即可。

# ex11

相应的要求已在代码里做出更改。
我们没有真正的复制字符串，只是让指针指向了同一个字符串。

# ex12

与之不同的运算符有｜｜或 ^非等
第一个判断不正确，第一个参数是执行可执行文件的命令，而非用户输入的第一个参数。

# ex13

见文件ex13a.c
使用for循环初始化，需要将i=0的情况放在for的初始化阶段，以后的赋值放在for的更新阶段。
switch语句更加简单，多重if语句易读性差。
如果break写在if的里面，会导致当第一个字母为y的时候，会输出default的语句，这不是我们想要的。

# ex14

不是真的需要，可以代入进调用的地方。
类似的还有isdigit isupper等等

# ex15

修改了一下ex14的代码。print_argument传入的是双重指针，print_letters传入的是单层指针。

# ex16
代码笔记：引入stdlib是为了使用malloc和free，引入string是为了使用strdup和strcpy。
strdup：输入字符指针，返回给你一个指针。输入的是要复制的字符串，输出的是其自己分配内存并且指向内存的指针，内存里是字符串。而strcpy需要输入两个指针，一个是要复制的，一个是你自己malloc的将来要使用的。strcpy返回void。并且需要你自己malloc和free。
在处理struct的时候要有对应的创建函数和释放内存的函数。不想显式释放内存又能避免内存泄露的办法是引入libGC库。你需要把所有的malloc换成GC_malloc，然后把所有的free删掉。
不使用指针，则先声明一个struct，之后用.来获取其member。
如果不使用指针传递的话，就需要pass by value，复制一份传递过去。
# ex17
strncpy的bug：当传入的字符串长度大于len的时候，会导致dst是不terminated的，也就是没有\0结尾，会导致在读取的时候读到越界的内存空间。可以手动将其设置成以\0结尾的。