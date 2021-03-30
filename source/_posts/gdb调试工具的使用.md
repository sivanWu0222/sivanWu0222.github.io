---
title: Golang中使用gdb调试工具
author: yirufeng
pin: false
toc: true
mathjax: false
sidebar:
  - blogger
  - webinfo
  - tagcloud
  - category
categories:
  - 工具
tags:
  - 调试
  - 工具
  - go
date: 2021-02-01 21:59:22
summary:
---


## gdb 工具的安装

!> 以mac系统为例


```shell
brew install gdb
```

## 使用场景
通常情况下，我们使用gdb是为了研究底层源码的实现，比如go中的runtime的入口在哪里？

## go中使用

1. 编译go程序的时候，要带上编译信息，也就是使用命令`go build --gcflags=all="-N -l" .`
2. 执行命令 `gdb 二进制文件的名字` 就可以进入gdb调试了


## gdb 中常用命令的使用

{% note info, note info 注意：gdb中命令行有一个特点，如果我们在命令中没有任何输入按下回车将会重复上一个命令，但是有一些命令不会，例如run %}

- list：将当前行号所在的代码打印出来
- 加一个断点：`break 断点所在的行` 或者 `b 断点所在的行`。
- 当我们执行run命令之后将会停在第一个断点上，然后使用`next`或`ns`执行到下一行，如果想要跟踪进去调用函数的内部，我们使用命令`s`进入调用函数的内部，如果我们想要退出该函数了，我们可以使用命令`return`退出返回到调用代码的地方，如果想要查看某一个变量的值，可以使用命令`p 变量名`，如果我们不想每次都打印指定的变量，我们可以使用`info args`打印参数或`info locals`打印本地变量
next：执行下一行
![b613L7](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/b613L7.png)
![OnudWS](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/OnudWS.png)

- 使用`info files`命令，看到有一个entry point，也就是代码是从这里开始执行的，我们想要让代码在入口停下，我们在入口加上一个端点，也就是在entry point对应的值的地址加上断点`b 对应的地址`
![rAQI5Q](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/rAQI5Q.png)

- list。列出代码，输入第一次后如果输入回车，那么就会重复以上命令
- break。加断点，一般是list之后，break 行号来加断点
-  bt。打印调用链
- info files。打印调试文件信息
- run。运行所要调试的代码
- up 和 down。在frame里跳来跳去
- info args 和 info locals 打印参数和本地变量
- whatis 和 p。打印变量和想要看的值，例如数组啊，函数啊，都可以
- info goroutines。查看所有的goroutine及其ID
- goroutine 命令。对对应的goroutine执行命令。
- q。退出
- help。打印帮助文档

## 参考

{% noteblock, 参考 %}
1. [安装教程](https://blog.csdn.net/github_33873969/article/details/78511733)
2. [使用教程](https://jiajunhuang.com/articles/2020_04_23-go_gdb.md.html)
3. [使用视频](https://www.bilibili.com/video/BV1rT4y1g77P?from=search&seid=6953978443203590763)

{% endnoteblock %}

<!-- more -->