
---
title: hexo环境搭建
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
  - hexo
tags:
  - hexo
date: 2019-03-21 21:17:42
summary:
---

> 本环境基于mac

注意：如果不想使用nvm进行多node版本的管理，直接可以使用去[官网]下载LTS版本，安装后从第四步开始执行

## 第一步：安装nvm

1. 执行`curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash` 或 `wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash`
    - 注意：注意后面的`v0.33.8`这是nvm的版本号，[最新版本查看](https://github.com/coreybutler/nvm-windows/releases)
2. 安装完成后关闭终端，然后键入nvm看一下是否有输出，如果`command not found`请[查看](https://www.jianshu.com/p/622ad36ee020)

补充：nvm常用命令介绍
```shell
● nvm install stable  安装最新稳定版 node
● nvm install <version>  安装指定版本，如：安装v4.4.0，nvm install v4.4.0
● nvm uninstall <version>  删除已安装的指定版本，语法与install类似
● nvm use <version>  切换使用指定的版本node
● nvm ls  列出所有安装的版本
● nvm alias default <version>  如： nvm alias default v11.1.0
```

## 第二步：卸载原来的hexo
1. 删除原来的hexo：`npm uninstall -g hexo-cli`

## 第三步：安装node
> 由于hexo必须要用npm安装，hexo安装的版本取决于node的版本

1. 使用nvm选择我们的node版本，如果对应版本没有安装，首先使用nvm安装对应版本的node。`nvm install <version>`
2. 切换到对应的node版本：`npm use <version>`

## 第四步：安装hexo
1. 安装hexo： `npm install -g hexo-cli`

参考：https://www.imczw.com/post/tech/hexo5-next8-updated.html
