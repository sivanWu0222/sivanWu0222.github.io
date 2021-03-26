---
title: ubuntu18.04 安装docker
author: yirufeng
top: true
toc: true
mathjax: false
summary: ubuntu18.04 下安装docker 并使用命令查看、
sidebar: [blogger, webinfo, tagcloud, category]
categories: 
    - docker
tags:
  - Ubuntu
  - docker
date: 2020-09-23 08:41:19
---
## 环境
本操作基于如下环境：
1. ubuntu 18.04
   
## 使用ubuntu安装docker

{% folding cyan, 卸载之前的docker %}
1. `sudo apt-get remove docker docker-engine docker.io containerd runc`
{% endfolding %}

{% folding cyan, 安装Docker %}
1. `sudo apt-get update`
2. `sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common` ：安装依赖包
3. `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -` ：添加 Docker 的官方 GPG 密钥
4. `sudo apt-key fingerprint 0EBFCD88` ：验证您现在是否拥有带有指纹的密钥
5. `sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"` ：设置稳定版仓库
{% endfolding %}

{% folding cyan, 安装 Docker Engine-Community %}


1. `sudo apt-get update` ：更新
2. `sudo apt-get install docker-ce` ：安装最新的Docker-ce
3. 启动：
   1. `sudo systemctl enable docker` 
   2. `sudo systemctl start docker` 

{% endfolding %}

{% folding cyan, 测试 %}

1. `sudo docker run hello-world` 

{% endfolding %}

<!-- more -->

## docker安装后使用遇到的问题：
> docker 权限问题 `Got permission denied while trying to connect to the Docker daemon socket at 。。。`

**解决方法：**通过将当前用户添加到docker用户组可以避免之后的命令使用sudo
{% folding cyan, 查看步骤 %}

1. `sudo groupadd docker` ：添加docker用户组
2. `sudo gpasswd -a $USER docker` ：将登陆用户加入到docker用户组中
3. `newgrp docker` ：更新用户组

{% endfolding %}


