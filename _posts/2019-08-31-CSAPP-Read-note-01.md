---
layout: post
title: 【读书笔记】《深入理解计算机系统》读书笔记(一)
categories: Reading-notes
description: 读书笔记
keywords: [读书笔记,CSAPP]
---

## 计算机系统漫游：从一段C代码说起

```c
#include <stdio.h>

    int main()
    {
        printf("Hello World! \n");
        return 0;
    }
```
对人来说，这其实是一段很简单的c语言代码；不过对于计算机而言，这是一段无法理解的符号，因此想让这段代码能够运行在计算机上面，需要整个系统的各个部分紧密协调的配合才能实现。首先，最需要做的事情是让我们的计算机能理解这段代码想让它做什么。换句话说，它只能理解“010101”这样的指令，那么我们就需要把上面的代码转换为“01”这样的机器码，那么实现这样转换的过程一共是四步：

* 预处理：把应用的头文件插入程序中
* 编译：把源程序代码处理成汇编指令
* 汇编：将上一步的汇编指令翻译成机器代码
* 链接：将引用到的库以某种方式合并到源代码中，比如上面代码中的printf函数

最终才能生成可运行的二进制文件。

刚才说到，计算机要运行一段代码需要软硬件协同合作才能实现，那么就需要知道计算机系统有哪些软硬件：

* 系统总线:携带数据在各个部件之间传输
* I/O设备：系统与外界交流的的通道
* 主存：临时存储程序和数据的设备
* CUP：中央处理单元，执行或者解释主存中的指令的设备
* 当然外设和磁盘也不能少

我们在终端输入运行指令`./hello.o`并敲下回车键时，操作系统会协助shell程序把hello.o二进制可执行代码和数据复制到主存中，这时候处理就可以按照二进制指令的命令把主存中的“Hello World！”复制到CPU内部的寄存器，再由寄存器复制到外设的显示设备，最终呈现到用户的屏幕上。

整个运行的过程中，经历了多次复制。其中，CUP读写寄存器的速度要比从主存读写快了100倍；此外，从磁盘中读写的速度更是比从主存中读写的速度慢了1000万倍。由此，高速缓存诞生了，它的速度介于CUP寄存器和主存之间，容量也是同样的道理。它只存储近期CUP可能会再次用到的热点数据，以此减少CUP等待的时间，提高程序的运行效率。

随者需求的不断增大，计算机能做的事越来越多，效率看得也越来越重，我们就希望计算机可以同时运行多个程序，通过时间片轮转，我们就可以“同时”的运行多个程序或者说进程。那么问题又来了，每次切换时间片都需要花费很大开销，这时候线程就产生了，在切换线程时要比切换进程节省开销。这里涉及到两个知识：

* 进程：操作系统对正在运行的程序的一种抽象
* 线程：进程中可以单独处理任务的模块的抽象

我们的计算机可能不止有一个CUP，可以有多个，变成了多核心计算机，这时候我们同一时间就可以运行多个程序，实现真正意义上的并行处理。

此外，我们通过在不同计算机之间架设IO设备，使得几台计算机可以互连，形成了网络，通过网络IO也可以实现程序的运行，只是说其它的计算机充当了自己CUP的一部分或者磁盘或者显示器，通过网络之间数据的传输和处理构成了我们的形形色色、多姿多彩的的计算机世界。