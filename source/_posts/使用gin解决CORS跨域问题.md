---
title: 使用gin解决CORS跨域问题
author: yirufeng
pin: false
toc: true
mathjax: false
categories:
  - golang
tags:
  - 跨域问题
  - CORS
date: 2020-12-21 19:42:46
---
## 什么是跨域问题

相同的源：如果两个页面的协议端口还有主机都相同，那这两个页面具有相同的源。

{% noteblock, 跨域问题 %}
在请求发送之前，浏览器会先发送一个option请求给gin项目，之后gin项目需要告知浏览器授权访问的协议，端口或者方法，浏览器接收到后台传给前台的访问的这些控制信息，再进一步是否决定正式请求。 option请求也叫域请求

{% endnoteblock %}

## 如何解决跨域问题
对于跨域问题的几种解决方法：
1. 包括前端的jsond 和 反向代理
2. 后端的api拦截option请求返回浏览器一个控制信息

{% note info, 如下代码是使用gin自己封装了一个CORS中间件进行跨域 %}

{% folding green, 使用gin解决跨域问题 %}

```go
func CORSMiddleware() gin.HandlerFunc {
	return func(ctx *gin.Context) {
		fmt.Println("--------------------------允许跨域中间件--------------------------")
		//通过中间件给header写入允许访问的域名或方法
		//这里最后一个参数传入的是允许前台的域名访问，因此最后一个参数应该是前台的域名加端口
		//ctx.Writer.Header().Set("Access-Control-Allow-Origin", "http://localhost:63343/")
		ctx.Writer.Header().Set("Access-Control-Allow-Origin", "*")
		//设置缓存时间
		ctx.Writer.Header().Set("Access-Control-Max-Age", "86400")
		//指定方法，可以传入POST以及GET，但是也可以用*代替所有的方法
		ctx.Writer.Header().Set("Access-Control-Allow-Methods", "*")
		ctx.Writer.Header().Set("Access-Control-Allow-Headers", "*")
		ctx.Writer.Header().Set("Access-Control-Allow-Credentials", "true")

		//如果option请求直接返回200
		if ctx.Request.Method == http.MethodOptions {
			ctx.AbortWithStatus(http.StatusOK)
		} else {
			ctx.Next()
		}
	}
}

```
{% endfolding %}

<!-- more -->

## 参考

1. [参考](developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy)
2. [参考](https://cloud.tencent.com/developer/article/1046663)




