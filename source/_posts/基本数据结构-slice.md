---
title: 基本数据结构-slice
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
link: 'https://xaoxuu.com/blog/2017-07-05-hexo-blog/'
date: 2021-02-10 09:29:35
summary:
---

## 基本概念
{% note info, note info 默认主题色，适合中性的信息 %}
{% noteblock, 结构剖析 %}


## 查看slice结构

> 查看slice内部的构造：在/src/runtime/slice.go中查看源码
```go
// runtime/slice.go
type slice struct {
    array unsafe.Pointer // 元素指针
    len   int // 长度 
    cap   int // 容量
}
```

看到了吗，slice 共有三个属性： 指针，指向底层数组，同一个底层数组可以被多个slice同时执行，因此只要有一个slice修改，其他指向该底层数组的slice也会被修改； 长度，表示切片可用元素的个数，也就是说**使用下标对 slice 的元素进行访问时，下标不能超过 slice 的长度**； 容量，底层数组的元素个数，容量 >= 长度。**在底层数组不进行扩容的情况下，容量也是 slice 可以扩张的最大限**。

![内部结构](https://mmbiz.qpic.cn/mmbiz_png/ASQrEXvmx62xDdibczAuO3uA8KfLTeatoyQNMQFBg6GITW701xSNL0icN178ymHFK7qh0gG6dVAPWCP4ociaxo0xg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

{% endnoteblock %}

## 切片的三种状态

> 平常我们创建切片的方式有很多种，其实里面就包含了我们切片中的三种状态，只是很少有人关注罢了


表格

nil slcie | empty slice | zero slice
new()  | make | 
只是声明 | 声明并赋值一个空切片

{% noteblock, 切片的三种状态 %}

1. nil 切片和空切片很相似，长度和容量都是0，官方建议尽量使用 nil 切片。
2. 创建出来的nil slice，它的长度和容量都为0。指向数组的指针也是0。这里比较混淆的是empty slice，empty slice的长度和容量也都为0，但是所有的空切片的数据指针都指向同一个地址 0xc42003bda0。空切片和 nil slice 比较的结果为false。

!> 参考：[码洞《深度解析 Go 语言中「切片」的三种特殊状态》](https://juejin.im/post/5bea58df6fb9a049f153bca8)
{% endnoteblock %}



{% folding green, 查看代码测试 %}

{% endfolding %}

看到了吗，slice 共有三个属性： 指针，指向底层数组； 长度，表示切片可用元素的个数，也就是说使用下标对 slice 的元素进行访问时，下标不能超过 slice 的长度； 容量，底层数组的元素个数，容量 >= 长度。在底层数组不进行扩容的情况下，容量也是 slice 可以扩张的最大限度。


## 参考
1. [深度解密Go语言之Slice](https://mp.weixin.qq.com/s/MTZ0C9zYsNrb8wyIm2D8BA)
2. [码洞《深度解析 Go 语言中「切片」的三种特殊状态》](https://juejin.im/post/5bea58df6fb9a049f153bca8)