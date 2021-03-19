---
title: linux下的五种网络IO模型以及select/poll/epoll区别
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
date: 2021-03-19 13:25:51
summary:
---

> 作为常见面试题(网络模型)进行知识总结同时进行深入学习
1. 5种io模型（同步阻塞，同步非阻塞，IO多路复用，信号驱动IO，异步IO），
2. 然后select, poll，以及epoll都是多路复用，然后一步一步从我们的select，poll，谈到epoll
3. golang里面的io模型是什么样的

参考视频：https://www.bilibili.com/video/BV1Ka4y177gs/?spm_id_from=333.788.recommend_more_video.-1

## 什么是IO
i是指input，o是指Output

两种输入/输出：
1. 本地IO：电脑上输入用户密码，将信息打印到控制台上。
2. 网络IO：通过网络进行数据的传输。

## IO的过程
> IO的过程分为两个阶段
1. 等待
2. 数据传输


## 五大IO模型
> 具体选用哪种看场合，
1. 同步阻塞：
2. 同步非阻塞：
3. IO多路复用：
4. 信号驱动IO：
5. 异步IO：


### 第一种模型：阻塞型IO
生活中的案例：![NYmD6C](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/NYmD6C.png)
适用场景：简单型的IO

代码中的案例：`fmt.sprintf`


### 第二种模型：非阻塞型IO
生活中的案例：![j1yDfB](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/j1yDfB.png) 还有就是我们去邮箱取邮件，如果有就取上，没有就直接回去了。
适用场景：用户访问非常频繁，但是我们又想提高效率，所以就使用非阻塞型IO
分成两种：
1. 直男型：取了之后不存在直接返回
2. 假直男：频繁轮询，开销巨大。

### 第三种模型：信号驱动类型
暖男：不粘人
先建立信号和处理程序的关联，当收到该信号后，马上调用处理程序，在处理程序中再读取数据。

如果两个信号一起来了，那么先来后到，两个信号都是进了队列的，

### 第四种模型：IO多路复用
渣男-收割机

同时监控（os实现），只要有一个通道突然可以使用，马上选通进行访问，

IO多路复用其实是阻塞式的，同时监听，只要有不可用的就一直监听下去，直到有一路监通为止，之后再通过我们写的代码进行数据访问，这就是多路IO复用，

### 第五种模型：异步IO
伪男：使用代理人
![9wE9Q8](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/9wE9Q8.png)


### 效率比较
![vkxG9t](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/vkxG9t.png)
{% noteblock,  %}


{% endnoteblock %}


{% note info,  %}

{% note info, note info 默认主题色，适合中性的信息 %}
{% noteblock, 标题（可选） %}

Windows 10不是為所有人設計,而是為每個人設計

{% endnoteblock %}

{% folding green, 查看代码测试 %}

{% endfolding %}


## 多路复用面试分享：

1. 说一下这个BIO，聊一下它的缺点

BIO中的B指的是blocking，作为服务器端开发的话，我们使用ServerSocket绑定完端口之后，我们会监听该端口，等待accpet时间，accept会阻塞当前主线程， 当我们收到accept事件之后，程序就拿到一个客户端与当前服务端的连接的socket，针对于这个socket我们都可以进行读写，但是针对于这个socket的读写方法都是会阻塞当前线程的，我们一般会使用多线程的方式来进行C/s交互，但是这样很难做到C10K，比如说1w客户端就需要服务端1w个线程去支持，这样cpu肯定会负载巨高，而且线程上下文切换，也会把机器负载拉满。

解释一下c10k：C10K 就是 Client 10000 问题，即「在同时连接到服务器的客户端数量超过 10000 个的环境中，即便硬件性能足够， 依然无法正常提供服务」，简而言之，就是单机1万个并发连接问题。这个概念最早由 Dan Kegel 提出并发布于其个人站点（ http://www.kegel.com/c10k.html ）。

2. C10k这个问题说明BIO肯定难顶了，就得靠NIO了，那你说NIO它靠什么解决的C10K问题呀：
我们站在java的角度看，nio包给我们提供一套非阻塞的接口，这样不需要为每一个c/s长连接保留一个单独的线程，阻塞IO之所以要给每个socket长连接指定一个线程，就是因为它阻塞， 现在这个NIO api已经具备了非阻塞的特性，就可以用1个线程去检查n个socket，

后面没看完，参考视频https://www.bilibili.com/video/BV1qV41167zC