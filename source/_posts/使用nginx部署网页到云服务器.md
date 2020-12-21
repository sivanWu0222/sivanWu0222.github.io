---
title: 使用nginx部署网页到云服务器
author: yirufeng
pin: false
toc: true
mathjax: false
categories:
  - nginx
tags:
  - nginx
  - 部署
  - 云服务器
date: 2020-10-24 09:56:00
summary:
--- 

{% note info, 最近腾讯云上88一年入手了一台云服务器，希望可以部署自己的项目或者静态网站，顺便学习一下部署相关的操作 %}

## 环境介绍
{% noteblock, 环境 %}
腾讯云服务器：centos 7.5 64位
本地环境：Mac OS
{% endnoteblock %}

<!-- more -->


{% noteblock, 需要辅助工具： %}
1. 可以远程连接服务器的工具，这里使用的是**secure CRT**
2. 可以上传文件到服务器的工具，这里使用的是**FileZilla**
{% endnoteblock %}

## 具体步骤
{% folding green, 步骤 %}
1. 服务器上新建要存放静态资源的目录，假设为`/root/html`
2. 使用`FileZilla`将静态资源从本地上传到`/root/html`
3. 安装nginx并进行配置(修改`/etc/nginx/nginx.conf`文件)
   1. 修改`user nginx;`为`nginx root;` 避免之后用户访问静态网页出现permisson denied
   2. ![g4NZun](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/g4NZun.png)
4. 域名解析到服务器
{% endfolding %}


## 参考链接
[nginx安装并进行配置](https://blog.csdn.net/gudada010/article/details/105558987)

