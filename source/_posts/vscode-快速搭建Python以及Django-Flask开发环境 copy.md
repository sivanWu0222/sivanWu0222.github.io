---
title: 'vscode 快速搭建Python以及Django,Flask开发环境'
author: yirufeng
top: true
cover: true
toc: True
mathjax: false
summary: 如何使用vs code快速搭建python开发环境，python中的基本配置，以及开发django和flask的环境
categories: Tools
tags:
  - vs code
  - Tools
date: 2020-06-04 09:22:19
---



# 快速搭建python 开发环境

安装扩展(汉化扩展->python)



> `Command + shift + p`进行快速设置

> `Alt + shift + f` 自动按照pep8规范进行格式化代码，如果没有安装，将会提示进行安装

> ` control + shift + ~ ` 快速新建一个终端



## 创建虚拟环境

![image-20200604092852093](https://gitee.com/yirufeng/images/raw/master/img/image-20200604092852093.png)

## 配置python解释器路径的三种方式：

1. 使用command + shift + p ![image-20200604093023126](https://gitee.com/yirufeng/images/raw/master/img/image-20200604093023126.png)
2. vscode 左下角选择运行环境![image-20200604093059999](https://gitee.com/yirufeng/images/raw/master/img/image-20200604093059999.png)
3. 直接在项目文件夹下的.vscode中的settings.json文件中进行配置![image-20200604092936231](https://gitee.com/yirufeng/images/raw/master/img/image-20200604092936231.png)



# 创建Django或者Flask开发环境

1. 创建虚拟环境 `python -m venv .venv`
2. 在虚拟环境下安装 pip install django/flask
3. ![image-20200604093227185](https://gitee.com/yirufeng/images/raw/master/img/image-20200604093227185.png)



注意：编写html文件的时候，生成的html文件没有任何内容，我们可以输入`!`，选中智能提示之后将会给我们生成一个html5的模板文件

![image-20200604093515497](https://gitee.com/yirufeng/images/raw/master/img/image-20200604093515497.png)

# 



# Mac 下的 vs code快捷键



[mac](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf)

[linux](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-linux.pdf)

[windows](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf)

# 参考

[pythonNav](https://pythonav.com/wiki/detail/1/83/)