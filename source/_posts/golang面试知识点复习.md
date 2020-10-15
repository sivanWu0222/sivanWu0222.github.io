---
title: golang面试知识点复习
author: yirufeng
pin: true
toc: true
mathjax: false
categories:
  - go
tags:
  - go
  - 源码
  - 面试
date: 2020-10-14 19:59:56
summary:
---
## 知识点
1. go的channel 有缓存无缓存如何定义，区别
2. goroutine内存泄漏场景
3. go并发为什么快
4. go垃圾回收
5. go协程 java线程区别
6. go数组和slice的区别
7. sync.Once的实现原理（上次哔哩哔哩面试问到了，幸亏我事后看了一眼是类似双重检验锁的实现方式哦），让我写出来，我写出了一大半，还让我运行一下，我运行不出来
8. context包有没有用过，我说没用过
9. 进程，线程，携程的区别
10. go中导致内存泄漏的原因
11. gin框架如何实现，我说用go内置的net http包实现的
12. 逃逸分析讲一下
13. 了解内存泄漏吗？有什么危害？
14. 一道很简单的Go题目，Go怎么做深拷贝。
15. Map是线程安全的吗？怎么解决并发安全问题
16. sync.Map 怎么解决线程安全问题？看过源码吗？
17. copy是操作符还是内置函数
18. slice的底层原理
19. map的底层原理
20. Go里面一个协程能保证绑定在一个内核线程上面的。
21. defer的执行顺序
22. defer A ; defer B ; defer panic("") A和B能不能执行到
23. Go切片和数据之间的区别
24. 切片怎么扩容,扩容过程中需不需要重新写入
25. Go的协程可以不可以自己让出cpu
26. Go的协程可以只挂在一个线程上面吗
27. 一个协程挂起换入另外一个协程是什么过程？
28. golang中channel调用问题，
29. 切片和数组区别和底层
30. defer recover panic 执行顺序
31. go多线程





{% note info, note info 默认主题色，适合中性的信息 %}
{% noteblock, 标题（可选） %}

Windows 10不是為所有人設計,而是為每個人設計

{% endnoteblock %}

{% folding green, 查看代码测试 %}

{% endfolding %}