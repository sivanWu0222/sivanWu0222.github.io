---
title: golang中关于文件的操作
author: yirufeng
pin: false
toc: true
mathjax: false
summary: golang中关于文件的操作
sidebar: [blogger, webinfo, tagcloud, category]
categories:
  - go
tags:
  - go
  - 文件
date: 2020-10-06 19:15:30
---


## 终端读写操作
{% note info, note info 默认主题色，适合中性的信息 %}
{% noteblock, 终端读写相关的文件句柄常量 %}
1. `os.Stdin`  标准输入
2. `os.Stdout` 标准输出
3. `os.Stderr` 标准错误
以上3个都是以文件的形式存在
{% endnoteblock %}
