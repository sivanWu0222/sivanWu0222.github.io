---
title: jwt-go的使用
author: yirufeng
pin: true
toc: true
mathjax: false
categories:
  - jwt
  - go
tags:
  - go
  - jwt
  - jwt-gos
date: 2020-12-13 19:57:08
---

## 了解JWT
> jwt简称为json web token，一般用于用户认证(前后端分离/微信小程序/app开发)。紧凑且安全，特别适合分布式站点的单点登录

JWT通常用在请求头的`Authorization`字段中，形如：`Authorization: Bearer <token>`，也是最标准传递JWT的方法

![tVw6eU](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/tVw6eU.png)

![KRB23n](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/KRB23n.png)


### JWT的构成

1. header: 指明了加密算法的类型以及JWT的类型，因为是JWT所以类型固定写成"JWT"
```
{
  "alg": "HS256",
  "typ": "JWT"
}
```
加密步骤：首先将json转换成字符串，然后使用base64加密，然后将加密后的字符串中的+号使用_代替，以及里面的\用某个字符替代
![nnkiJ5](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/nnkiJ5.png)
2. payload: 是我们存放有效信息的载体
加密步骤：首先将json转换成字符串，然后使用base64加密，然后将加密后的字符串中的+号使用_代替，以及里面的\用某个字符替代
![EmK6Sb](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/EmK6Sb.png)
3. verify signature: 
  1. 第一部分加密后的密文与第二部分的密文拼接起来，形成s1
  2. 对s1使用第一部分头部指定的algo方法进行加密，同时进行加盐，此时生成一个密文s2
  3. 对s2加密之后的密文再做base64Url加密
  4. 返回给用户
  5. 公式如下：HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret_key)
具体：第一部分的密文与第二部分的密文拼接起来，对前两部分的密文进行第一部分指定的algo方法(上面是hs256)的加密，同时进行加盐(就是放一个随机字符串，确保加盐其他人不知道，对我们来说很重要)。此时生成一个hash256的密文。对hash256加密之后的密文再做base64url加密

注意：由于base64编码是可逆的，因为payload中存的信息是可逆的base64编码，所以第二部分的数据上实际上是可以逆向到明文的，因此我们不可以在payload中存放隐私数据。

以后用户再来访问的时候都需要携带token，后端都需要对token进行校验

校验步骤：
			i. 获取token
			ii. 第一步：对token切割
			iii. 第二步：将第二段进行base64url解密，拿到payload，验证payload中的超时时间是否超时
			iv. 第三步：验证第3段合法性（md5以及hash256都不可以反解），将第1，2段进行algo指定的方法(上面是hs256)加密，加密之后得到密文与我们第3段通过base64url解密之后的字符串进行比对，如果密文相等表示token没有修改过，此时认证通过

如果将payload中的超时时间修改了，即使前两段认证通过，但是第3段我们是对第一段以及第二段中的内容进行加密，所以第三段验证将不会通过，即使用户修改了第3段，但是没有我们的盐值也是无法进行修改成和我们一样的。

使用的时候如何做？
已经有模块帮我们做了，内部已经帮我们实现了对应的算法，

真正在企业里面如何使用：一般都是集成在中间件中，我们用户想访问一些需要登录的接口将会访问对应的中间件，例如我们的jwt中间件


### JWT验证执行流程

普通的token验证执行流程：
	1. 首先用户进行登录，服务端生成一个token，服务端自己保存一份，之后服务端会将token返回给前端或浏览器，每个token都有一个对应的失效时间
	2. 之后用户所有的操作都会携带token去访问，我们服务端都要对这个token进行校验。


JWT的执行流程如下：
1. 用户提交用户名以及密码进行登录，如果登录成功，创建一个JWT返回给用户
注意：使用JWT生成的token是由3段字符串组成的，中间通过"."进行连接，
  第一段字符串：叫做header,

一般我们写后台登录只需要用session以及cookie就可以了，但是如果要前后端分离需要使用jwt

## golang中使用JWT进行校验

> 这里我们使用gin结合jwt-go将其封装成一个中间件，对需要进行认证的使用该中间件进行认证


```go


```

## 补充
[参考](https://www.guhei.net/post/jb871)
之前在一个帖子上看到说数据库中每一个用户的salt值都是随机的，用户注册的时候生成并且存入数据库中，
注册执行过程如下：
1. 获取用户的明文密码
2. 随机生成一个salt
3. 将salt以及用户使用的salt加密后的密码存入到数据库中，因为进行登录的时候我们还需要使用salt进行认证

登录执行过程如下：
1. 获取用户输入的密码
2. 去查询数据库对应的salt
3. 对用户密码使用salt进行加密并与数据库中的密文进行比对

## 使用方式
![FNgi6I](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/FNgi6I.png)

## JWT的特点
![rJ5ZuX](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/rJ5ZuX.png)


## 参考
[武佩奇老师的jwt课程](https://www.bilibili.com/video/BV1tJ411B7yJ?p=4)
[阮一峰老师的jwt讲解](https://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html)
[使用gin + jwt-go实现restful api校验](https://mojotv.cn/go/golang-jwt-auth)
[JWT内部具体如何做的](https://blog.csdn.net/houmenghu/article/details/99181326)
[JWT-GO中间件使用参考](https://studygolang.com/articles/13062)


{% note info, note info 默认主题色，适合中性的信息 %}
{% noteblock, 标题（可选） %}

Windows 10不是為所有人設計,而是為每個人設計

{% endnoteblock %}

{% folding green, 查看代码测试 %}

{% endfolding %}




