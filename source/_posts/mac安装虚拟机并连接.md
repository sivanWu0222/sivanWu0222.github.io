---
title: Mac安装Ubuntu并且通过secureCrt连接
author: yirufeng
top: false
cover: true
toc: true
mathjax: false
summary: 使用Parallels Desktop安装本地下载好的ubuntu镜像，并通过secureCrt连接
sidebar: [blogger, webinfo, tagcloud, category]
categories: 
  - 工具篇
tags:
  - 工具
  - tools
date: 2020-09-11 09:08:18
---
> 最近准备复习操作系统的知识并通过linux实践，索性就安装了一个ubuntu虚拟机吧
> 以下将parallels Desktop简称为PD

## 准备工作
1. 离线下载好的ubuntu对应版本的iso镜像
2. secureCrt工具
3. PD软件


## 步骤
1. 通过PD安装下载好的ubuntu对应版本的iso镜像
2. 安装之后ubuntu虚拟机已经帮助我们启动了，但是我们通过`ifconfig`查看ip之后，通过secureCrt连不上对应的ip。
   1. 解决方案：在Ubuntu终端中输入`sshd`，验证是否已安装该包。若未安装则通过`sudo apt-get install openssh-server`
3. 之后便可以发现通过ip地址和密码在secureCrt上进行登录


## 参考
1. [参考文章](https://blog.csdn.net/lee_x_lee/article/details/82984707)

<!-- more -->