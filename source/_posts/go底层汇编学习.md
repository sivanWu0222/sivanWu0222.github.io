---
title: go底层汇编学习
author: yirufeng
pin: true
toc: true
mathjax: false
sidebar:
  - blogger
  - webinfo
  - tagcloud
  - category
categories:
  - null
tags:
  - null
  - null
date: 2021-02-10 09:53:33
summary:
---


虽然go汇编是plan9汇编，但是x86汇编与go汇编是相通的


程序编译的几个阶段：
编译->汇编->优化—>链接

一个代码经过汇编之后就生成了汇编代码，然后经过编译器优化，优化之后进行链接，最终得到可执行程序。这里说的优化是汇编优化，实际上我们在所有阶段都有优化，

编译的话，广义上就是指的我们整个编译阶段，狭义上的话就是说词法语法分析，但是这个阶段还没有编译

我们虽然写的是汇编代码，但是实际上机器指定的是0101的这些序列，这就是汇编和机器码的关系，
指令集是指一般每一种cpu都会给你提供你可以用的指令的集合，


指令寄存器存储的是我们代码的地址，CPU在解释指令的时候是一条一条解释的，没有并发这种。通过这个地址器中的值就可以找到我们的指令，
![tOtEGa](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/tOtEGa.png)
大部分不需要理解，知道通用寄存器，和简单的扩展寄存器就可以了。

![XiuoUM](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/XiuoUM.png)

![Hd4Pvj](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/Hd4Pvj.png)

函数调用都是有一个函数栈存在的，有函数栈的话可能会发生栈溢出，函数栈是同一个函数跳转或者调用另外一个函数的时候栈的结构，将参数按照规范传入到栈上，然后在被调用函数中我们可以读取到被传入的参数，然后用这些参数做一些业务逻辑，最后返回值。


intel cpu上栈是从高地址向低地址生长


被调用函数如何读取到形参：
在老的平台上所有参数都是通过栈传入，我在传入的时候其实只需要读这个位置就可以了，后来到了64位平台上寄存器数量变多，c语言很多编译器尽量在参数比较少的函数调用的时候做优化，会将我们前6个参数传入到寄存器中，我具体用哪几个寄存器都是约定好的，在栈上都是只传第7个以后的参数，

每一个函数都有自己的栈，栈一般用rbp(栈的栈底，frame base pointer)以及rsp(栈的栈顶，很靠近调用该函数的位置，所以直接向上找到输入参数即可)描述

![8wykgm](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/8wykgm.png)

我们之前原来函数的时候在上半部分搞事情，调用函数的时候，会将调用者下一条指令传入到return address这个地方中，之后被调用函数执行完之后拿到原来的地址返回去执行下一条指令，之后被调函数的栈其实进行了名义上的销毁，并没有改变值，只是我们后面执行其他指令的时候如果栈往下增长就会将原来的内容进行覆盖，64位系统中，6个参数以内的都是通过寄存器传入，6个以外的通过函数栈传入，


任意的编程语言自己分配内存的时候，其实都需要自己对这段内存清0，os并不保证，go语言中其实都是给我们赋0，回收的时候并不需要自己赋值0，成本太高。

通用寄存器是16个


`section .data`表示我的代码是在哪个段，

执行syscall也就是进行了系统调用将会切换到内核态


我们写的go执行成plan9的汇编，最终还是要转换到各个平台的汇编


`MOVB $1, DI -> mov al, 0x44    // 1 byte`
英特尔的汇编是将44挪到al里面，然后前面我们的plan9表示将1挪到DI里面。
![VLnpdK](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/VLnpdK.png)


程序有基本的分段概念，text段就是代码存储的位置，也就是代码段
![bOpvBM](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/bOpvBM.png)
jbe指令是大于等于就跳转，`sub rsp, 0x18`是将rsp(因为是我们的栈顶)向下移动

lea是做地址运算的(自己查到的：取有效地址，也就是取偏移地址)
call是调用函数
mov是移动
JNE是不等于就跳转

生成两种汇编形式的代码：![GfQE0l](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/GfQE0l.png)哪种舒服用哪种

golang中函数的参数以及返回值都是通过函数栈来进行的


我们写的代码最终都在编译的时候转成内部函数，make一个slice和make一个map编译后底层都是不一样的，所以我们可以通过反汇编进行分析


![rnQDh8](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/rnQDh8.png)

go会根据逃逸分析进行分配确定是在堆还是栈中，

![Jk0t5m](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/Jk0t5m.png)



程序在编译之后，编译器会在编译后的代码的函数头部和函数尾部插入两段调度指令，中间是逻辑
![FY9kqB](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/FY9kqB.png)



nosplit表示go的栈空间不够就会重新分配，顺便将执行权交给runtime，（如果我们非常确定栈的大小之后编译器就不会在函数头部和尾部插入调度指令，这两段指令就是为了检查我们是否需要扩容，如果扩容就跳到插入到函数尾部的代码等）
为什么会重新分配：一个g初始化之后，栈只有2kb，慢慢随着深层次函数的递归调用会慢慢增长，增长到一定程度会溢出。


汇编的结果是`go tool compile -S 文件名.go`
反汇编是使用`go build`生成的.o文件，然后使用`go tool objdump 我们的二进制文件`


![xXsAHN](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/xXsAHN.png)

如果我们线上遇到问题，可以pprof(1.11将火焰图都集成进去了 )指令看到goroutine栈以及死锁之类的，帮助我们快速定位问题。

{% note info, note info 默认主题色，适合中性的信息 %}
{% noteblock, 标题（可选） %}

Windows 10不是為所有人設計,而是為每個人設計

{% endnoteblock %}

{% folding green, 查看代码测试 %}
{% endfolding %}

{% folding cyan open, 查看默认打开的折叠框 %}

{% endfolding %}

## 参考
1. [深入Go的底层，带你走近一群有追求的人](https://mp.weixin.qq.com/s?__biz=MjM5MDUwNTQwMQ==&mid=2257483728&idx=1&sn=c881e621f4f2dfc0ff2277766249ced5&chksm=a5391706924e9e103424852355327c991a7ea2b939add74b949a1ed2cc7fbe62dc79c08efe2f&scene=21#wechat_redirect)
2. [GO汇编-函数](https://www.cnblogs.com/binHome/p/13034103.html)
3. [汇编常用指令](https://blog.csdn.net/qq_36982160/article/details/82950848)
4. [曹大的文章](https://github.com/cch123/asmshare/blob/master/layout.md)