---
title: Golang连接mysql插入时间早8个小时
author: yirufeng
top: true
toc: true
mathjax: false
sidebar: [blogger, webinfo, tagcloud, category]
categories: 
  - Golang
keywords: Golang
tags:
  - Mysql
  - Golang
date: 2020-09-24 09:48:42
---
## 问题描述
使用Golang操作Mysql，插入一条记录后发现新插入的时间比当前时间早了8个小时。
1. {% note warning, 使用go-sql-driver来连接mysql数据库，获取的时区默认是UTC +0的，与本地的东八区是有区别，在业务处理中会出现问题 %}
2. {% note warning, 获取mysql中的日期，是string类型，需要在代码中用time.Parse进行转化 %}

## 解决方案
{% note success, 在连接的dataSourceName中加上`parseTime=True`和`loc=local`。此处的`local`可以更换为具体的时区`(Asia/Shanghai)` %}

{% folding red, 修改之前的dataSourceName %}
```go
dataBaseSource := fmt.Sprintf("%s:%s@tcp(%s:%s)/%s?charset=%s", 
    MysqlUser, MysqlPassword, MysqlHost, MysqlPort, MysqlDatabase, MysqlCharset)
conn, _ := sql.Open("mysql", dataBaseSource)
```
{% endfolding %}


{% folding green, 修改后的dataSourceName %}
```go
dataBaseSource := fmt.Sprintf("%s:%s@tcp(%s:%s)/%s?charset=%s&loc=Local&parseTime=true", 
    MysqlUser, MysqlPassword, MysqlHost, MysqlPort, MysqlDatabase, MysqlCharset)
conn, _ := sql.Open("mysql", dataBaseSource)
```
{% endfolding %}


<!-- more -->