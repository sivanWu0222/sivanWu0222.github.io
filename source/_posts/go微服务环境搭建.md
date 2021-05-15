---
title: go微服务环境搭建
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
  - 微服务
  - 环境搭建
tags:
  - 环境搭建
  - 微服务
  - go
date: 2021-05-12 16:22:25
summary:
---

> 本环境基于Macos搭建，运行的go版本是1.14.11，由于原先使用的1.15.5依赖包对应版本不存在或冲突修改后无法解决，所以只能修改go版本


# 沈逸老师go-micro搭建环境


## 搭建成功使用命令:
1. `go get github.com/micro/go-micro/v2`
2. `go get github.com/micro/go-plugins/registry/consul/v2`

参考：https://segmentfault.com/a/1190000023529475

## 搭建过程中遇到的问题：

```go
# github.com/coreos/etcd/clientv3/balancer/resolver/endpoint
../../../../../go/pkg/mod/github.com/coreos/etcd@v3.3.25+incompatible/clientv3/balancer/resolver/endpoint/endpoint.go:114:78: undefined: resolver.BuildOption
../../../../../go/pkg/mod/github.com/coreos/etcd@v3.3.25+incompatible/clientv3/balancer/resolver/endpoint/endpoint.go:182:31: undefined: resolver.ResolveNowOption
# github.com/coreos/etcd/clientv3/balancer/picker
../../../../../go/pkg/mod/github.com/coreos/etcd@v3.3.25+incompatible/clientv3/balancer/picker/err.go:37:44: undefined: balancer.PickOptions
../../../../../go/pkg/mod/github.com/coreos/etcd@v3.3.25+incompatible/clientv3/balancer/picker/roundrobin_balanced.go:55:54: undefined: balancer.PickOptions
# github.com/micro/go-micro/transport/quic
../../../../../go/pkg/mod/github.com/micro/go-micro@v1.18.0/transport/quic/quic.go:54:12: q.s.Close undefined (type quic.Session has no field or method Close)
../../../../../go/pkg/mod/github.com/micro/go-micro@v1.18.0/transport/quic/quic.go:121:3: unknown field 'IdleTimeout' in struct literal of type quic.Config
```

解决办法：go.mod文件夹中加上下面两句话
```
replace google.golang.org/grpc => google.golang.org/grpc v1.26.0
replace github.com/lucas-clemente/quic-go => github.com/lucas-clemente/quic-go v0.14.1
```
参考：
1. https://github.com/etcd-io/etcd/issues/12124#issuecomment-674368288
2. https://github.com/etcd-io/etcd/issues/11931
3. https://www.codenong.com/cs109250534/
4. 推荐：https://studygolang.com/articles/32791


遇到的问题2：`panic: qtls.ConnectionState not compatible with tls.ConnectionState`

最后以上的问题都采用一种策略，删除原有go版本并且下载go1.14.11版本得到了解决，之后执行命令`go get -u github.com/micro/go-micro`
就是go的版本不要使用go1.15

# 租房网搭建环境：


## 前言
> 如果网速很慢，经常io timeout，可以执行如下两条命令：
```shell
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct
```


## 安装protoc
1. [下载可执行文件](https://github.com/protocolbuffers/protobuf/releases)
2. 下载完成后，解压到`~/Library`，并修改文件夹名字为proto，并进入终端配置系统环境变量：`export PATH=$PATH:$GOPATH/bin:$HOME/Library/protoc/bin`
3. 采用命令`protoc --verison`进行验证。配置成功，会有类似输出：`libprotoc 3.11.2`

## 安装protoc-gen-go

1. `go get -u github.com/golang/protobuf/protoc-gen-go`
2. 进入到目录：`cd $GOPATH/src/github.com/golang/protobuf/protoc-gen-go`
3. 执行`go build`，生成可执行文件`protoc-gen-go`
4. 将我们的可执行文件复制到`$GOPATH/bin`下：`cp protoc-gen-go $GOPATH/bin `

## 安装protoc-gen-micro
> 如果实在get不下来我们可以直接clone代码下来，然后`go build`
1. `go get github.com/micro/protoc-gen-micro`
2. 进入到`$GOPATH/src/github.com/micro/protoc-gen-micro`目录下，进行`go build`，生成可执行文件`protoc-gen-micro`
3. 将我们的可执行文件复制到`$GOPATH/bin`下：`cp protoc-gen-micro $GOPATH/bin `

测试：在goland中新建一个项目，并且编写一个proto文件夹，编写demo.proto文件并填入如下内容
```protobuf
syntax = "proto3";

package myproto;

option go_package = "./";


//客户端发送给服务端的
message HelloRequest {
  string name = 1;
}

//服务端返回给客户端的
message HelloResponse {
  string msg = 1;
}


//客户端向服务端发送的请求
message NameRequest {
  string name = 1;
}

//服务端返回给客户端的响应
message NameResponse {
  string msg = 1;
}



//定义服务
service HelloService {
  //函数的传入和传出就是我们自己要定义的消息类型
  //一个打招呼的函数
  rpc SayHello (HelloRequest) returns (HelloResponse) {}
  //一个说名字的函数
  rpc SayName (NameRequest) returns (NameResponse) {}
}

```

进入到proto文件所在的目录，执行`protoc --proto_path=$GOPATH/src:. --micro_out=. --go_out=. *.proto`
当前目录下生成两个文件：demo.pb.go和demo.pb.micro.go


## 安装go-micro
执行命令：`go get github.com/micro/go-micro/v2@latest`

## 安装micro


### 方式一：
执行命令：`go get github.com/micro/micro/v2@latest`

通过该方法安装的micro默认使用的服务发现组件是mdns，没有集成Consul、Etcd等第三方的组件。我们后面需要使用Consul，所以此时需要自己手动编译安装。
### 方式二：

1. `git clone https://github.com/micro/micro.git`
2. 进入到clone后的文件夹，新建plugins.go文件，键入如下内容：
```go
package main

import (
  _ "github.com/micro/go-plugins/registry/consul/v2"
)
```
3. 最后，重新编译安装：`go install`
4. 完成后，执行如下命令检查micro是否安装成功：`micro --version`。如果有类似输出说明安装成功：
```
micro version 2.0.0
```

### 方法三：直接下载二进制文件
1. 点击[下载地址](https://github.com/micro/micro/releases/tag/v2.0.0)进行下载
2. 将我们的`micro`可执行程序移动到`/usr/local/bin/`

<!-- more -->

## consul环境搭建

### docker安装consul集群
{% folding green, 自己动手采用docker搭建一个consul集群 %}
```shell
# 下载最新的consul镜像
docker pull consul
# 启动我们的第一个consul节点
docker run --name consul1 -d -p 8500:8500 -p 8300:8300 -p 8301:8301 -p 8302:8302 -p 8600:8600 consul agent -server -bootstrap-expect 2 -ui -bind=0.0.0.0 -client=0.0.0.0
# 获取我们第一个启动节点的ip地址：会输出一个ip地址
docker inspect --format '{{ .NetworkSettings.IPAddress }}' consul1
# 启动第二个consul节点
docker run --name consul2 -d -p 8501:8500 consul agent -server -ui -bind=0.0.0.0 -client=0.0.0.0 -join 这里是第一个节点的ip地址
# 启动第三个consul节点
docker run --name consul3 -d -p 8502:8500 consul agent -server -ui -bind=0.0.0.0 -client=0.0.0.0 -join 这里是第一个节点的ip地址
# 查看运行的容器
docker ps
```
之后访问`localhost:8050`就可以进入到我们的consul可视化界面
{% endfolding %}

### 本机(mac)安装consul：
1. 直接进入[consul官网](https://www.consul.io/downloads)，下载可执行文件，然后解压到`$HOME/Library/consul/`
2. 同时将路径加入到我们的系统环境变量`~/.bash_profile`中
```shell
# add consul
export PATH=$PATH:$GOPATH/bin:$HOME/Library/consul
```
3. 然后刷新我们的配置，此时使用consul执行命令`consul members`来查看我们docker搭建的consul集群

### consul相关命令补充：
1. ![vtxNib](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/vtxNib.png)
2. ![zT7xfI](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/zT7xfI.png)

### consul参考资料：
1. [参考1](https://www.liangxiansen.cn/2017/04/06/consul)
2. [参考2](https://blog.csdn.net.yuanyuanispeak/article/details/54880743)
3. [docker安装consul集群](https://www.jianshu.com/p/0fe826b7017f)


## 遇到的问题
1. ![uFB8I0](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/uFB8I0.png)：[参考解决方案](https://stackoverflow.com/questions/39665379/fully-qualified-import-path-in-auto-generated-code)
自己是在goland中新建一个Proto文件夹并编写了一个新的proto文件测试发现可以通过


## References
1. [macOS配置Go Micro开发环境](https://zhuanlan.zhihu.com/p/104488946?hmsr=toutiao.io)








"""
画图：

打平的图

横坐标的每一个值都是一个三元组，
    三元组的第1个值代表第1个通道的bin的内容，

一个是整体的图，所有图片的像素三元组都在这个线上
    bin=4：画出四条线：HSV,HLS,YUV,YCrCb
    bin=8：画出四条线：HSV,HLS,YUV,YCrCb
    bin=16：画出四条线：HSV,HLS,YUV,YCrCb
    bin=32：画出四条线：HSV,HLS,YUV,YCrCb
    bin=64：画出四条线：HSV,HLS,YUV,YCrCb

另外几个是有bin分组的图:
    bin=4：画一个图，三条线的内容如下
        好图：
        普通：
        效果不好：
    bin=8：画一个图，三条线的内容如下
        好图：
        普通：
        效果不好：
    bin=16：画一个图，三条线的内容如下
        好图：
        普通：
        效果不好：
    bin=32：画一个图，三条线的内容如下
        好图：
        普通：
        效果不好
    bin=64：画一个图，三条线的内容如下
        好图：
        普通：
        效果不好

一共画10个图
"""