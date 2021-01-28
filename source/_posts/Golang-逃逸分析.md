---
title: Golang-逃逸分析
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
  - go
  - 逃逸分析
tags:
  - 逃逸分析
date: 2021-01-28 18:26:04
summary:
---

## 两个命令知识
`$ go tool compile -m pro_2.go` 打印出编译器逃逸分析的过程，使用 -m 参数。

`$ go tool compile -S pro_2.go > pro_2.S`  **go tool compile -S pro_2.go用于反编译代码为汇编代码，**这里后面使用pro_2.S表示输出到pro_2.S文件中


## 逃逸分析
1. 核心：go语言编译器会自动决定把一个变量放在栈还是放在堆，编译器会做逃逸分析(escape analysis)，**当发现变量的作用域没有跑出函数范围，就可以在栈上，反之则必须分配在堆。**
2. 作用：go语言声称这样可以释放程序员关于内存的使用限制，更多的让程序员关注于程序功能逻辑本身。

## 具体流程
[参考](https://www.kancloud.cn/aceld/golang/1958306)

## 总结
Golang中一个函数内局部变量，不管是不是动态new出来的，它会被分配在堆还是栈，是**由编译器做逃逸分析之后做出的决定。**


<!-- more -->

