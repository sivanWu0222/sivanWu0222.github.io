---
title: golang中命令行参数的使用
author: yirufeng
pin: false
toc: true
mathjax: false
summary: golang中命令行参数的使用
sidebar: [blogger, webinfo, tagcloud, category]
categories:
  - go
tags:
  - go
  - flag
date: 2020-10-06 19:16:43
---
## 使用os.Args获取输入的命令行参数

{% noteblock, os.Args获取输入的命令行参数 %}
1. os.Args本质上是一个切片，会将命令行的内容全部读取，只是将我们输入的内容按照空格进行分割，其中第1个是执行的程序的全路径名称，
2. 后面的为我们执行的时候命令行上的其他参数
{% endnoteblock %}



{% folding green, 具体实现 %}
```go
package main

import (
	"fmt"
	"os"
)

/**
 * @Author: yirufeng
 * @Email: yirufeng@foxmail.com
 * @Date: 2020/10/6 6:00 下午
 * @Desc: os.Args练习


本质上是一个切片，会将命令行的内容全部读取，只是将我们输入的内容按照空格进行分割，其中第1个是程序执行的名称，
后面的为我们执行的时候命令行上的其他参数
 */
func main() {
	fmt.Println(len(os.Args))
	fmt.Println(os.Args)
}
```
{% endfolding %}

## 使用flag包来解析命令行参数
{% noteblock, 具体步骤 %}
1. StringVar，IntVar，BoolVar需要传入4个参数
   1. 第1个参数为接收值的变量的地址
   2. 第2个参数为我们使用命令行中的`-`后面紧跟的字符作为key
   3. 第3个参数为默认值
   4. 第4个参数为参数说明（命令设置错误时的说明）
2. 使用flag包解析命令行参数最后一定要记得`flag.Parse()`之后，我们上面的设置的解析参数才会生效
{% endnoteblock %}

{% note info, 到时候执行如下代码的时候，我们需要先使用`go build`指令进行编译，编译之后运行可执行文件的时候后面还得加上参数，这里我们应该加上`./demo -h 10.26.166.14 -p 3309 -o false` 如果不指定参数的时候我们将会使用程序中设置的默认值来读取 %}

<!-- more -->

{% folding green, 具体实现 %}
```go
package main

import (
	"flag"
	"fmt"
)

/**
 * @Author: yirufeng
 * @Email: yirufeng@foxmail.com
 * @Date: 2020/10/6 6:10 下午
 * @Desc: flag 包的使用
 */

func main() {
	var host string
	var port int
	var isopen bool
	//第1个参数为我们从命令行中获取对应的值之后要赋值给具体哪个变量，第2个为命令行输入参数对应的key，第3个为默认值，第4个为执行错误的说明
	flag.StringVar(&host, "h", "127.0.0.1", "the ip address of your machine")

	flag.IntVar(&port, "p", 3306, "the port of application")

	flag.BoolVar(&isopen, "o", false, "please check if database is open")
	//前面设置了之后，最后一定要加上这一句话才会解析我们命令行中的参数
	flag.Parse()

	fmt.Println("打印我们获取到的变量的内容：", host, port, isopen)
}

```
{% endfolding %}