---
title: centos7安装docker
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
    - docker
tags:
  - Centos
  - docker
  - Tools
  - Linux
date: 2021-03-30 10:14:34
summary:
---


{% note warning, 系统要求：docker支持64位Centos7，并且要求内核版本(查看Linux系统内核版本：`uname -r`)不低于3.10 %}

## 卸载旧版本


## 安装docker

{% folding green, 卸载旧版本 %}
{% note info, 旧版本的docker称为docker或docker-engine，使用如下命令卸载旧版本  %}
```shell
sudo yum remove docker docker-common docker-selinux docker-engine
```
{% endfolding %}


{% folding green, 安装方式一：使用yum安装 %}
```shell
sudo yum install docker-ce
```
{% note info, 如果执行之后返回的是没有可用软件包，此时我们需要安装必要的软件依赖以及更新增加docker-ce yum源 %}
```shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
执行完这两条命令之后我们便可以重新执行`sudo yum install docker-ce`进行安装
{% endfolding %}

{% folding green, 安装方式二：使用脚本安装 %}
{% note info, 在测试或开发环境中docker官方为了简化安装流程，提供了一套便捷的安装脚本，centos系统上可以使用这套脚本进行安装 %}
```shell
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh --mirror Aliyun
```
执行完这个命令之后，系统会自动将一切工作准备好，并把docker ce的Edge版本安装在系统中
{% endfolding %}

<!-- more -->

{% folding green, 启动docker CE %}
```shell
sudo systemctl enable docker
sudo systemctl start docker
```
启动docker之后可以使用`docker info`来查看docker的信息
{% endfolding %}

{% folding green, 建立docker用户组 %}
默认情况下，docker命令会用Unix Socket与Docker引擎通讯，但是只有root用户与docker组的用户才可以访问Docker引擎的Unix Socket。一般Linux系统上不会直接使用root用户进行操作，因此需要将使用docker的用户加入docker用户组。
```shell
sudo groupadd docker #建立 docker 用户s组
sudo usermod -aG docker ${USER} #将当前用户加入docker用户组
```
启动docker之后可以使用`docker info`来查看docker的信息
{% endfolding %}

{% folding green, 测试docker是否正确安装 %}
```shell
docker run hello-world #启动一个基于hello-world的镜像的容器
```
{% endfolding %}

## 配置docker镜像加速器

docker默认从docker hub上拉取镜像，国内会网速偏慢，建议配置镜像加速器

{% folding green, 步骤 %}
1. 在/etc/docker/daemon.json文件(如果没有就创建)中写入：这里配置的是自己的阿里云的，建议配置阿里云的（[获取镜像加速器地址](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors）
```
{
  "registry-mirrors": ["https://*(涉密省略)**.mirror.aliyuncs.com"]
}
```
2. 重新启动服务生效
```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
```
3. 查看一下docker默认下载镜像的地址：执行`docker info` ，查看Registry Mirrors的内容是否为我们配置的内容。如下图所示：![pODGrE](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/pODGrE.png)
{% endfolding %}

## 卸载docker
{% folding green, 删除docker安装包 %}
```shell
sudo yum remove docker-ce
```
{% endfolding %}

{% folding green, 删除docker镜像 %}
```shell
sudo rm -rf /var/lib/docker
```
{% endfolding %}
