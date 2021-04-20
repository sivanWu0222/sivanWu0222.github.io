---
title: parallel安装的虚拟机登录root
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
  - tools
tags:
  - tools
  - parallel desktop
date: 2021-04-04 15:13:49
summary:
---


{% note info, 打开terminal %}

1. 执行`sudo passwd`
2. 出现`Password:`，键入自己当前用户的密码
3. 出现`Enter new UNIX password：`这个是root的密码
4. 出现`Retype new UNIX password：`重复root的密码

**之后就可以使用su命令以及刚才设置的root用户密码切换到root用户**

<!-- more -->