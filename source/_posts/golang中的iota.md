---
title: golang中的iota
author: yirufeng
pin: false
toc: true
mathjax: false
sidebar: [blogger, webinfo, tagcloud, category]
categories:
  - go
tags:
  - go
  - iota
date: 2020-10-13 20:50:33
summary: golang中关于iota的用法
---



{% note info, note info 经常有面试官甩出个const以及iota的面试题 %}

如下便是国内某公司的面试题：
{% folding green, 回答输出结果 %}
```go
package main

import "fmt"

/**
 * @Author: yirufeng
 * @Email: yirufeng@foxmail.com
 * @Date: 2020/10/13 6:06 下午
 * @Desc:
 */


//第一个const来自于面试题
const (
	i = 7
	j
	k
)

const (
	a = 7
	b
	c = 3
	m = 1 << iota
	n
	o
)

func main() {
	fmt.Println(a, b, c)
	fmt.Println(i, j, k, m, n, o)
}
```
{% endfolding %}




## 总结

{% noteblock, 进行几点总结 %}

1. 不同 const 定义块互不干扰：**即我们在推算这些常量值的时候只需要看当前的 const ( ) 之内的内容，不用关心之前或之后是否也用常量定义语句**
2. 所有注释行和空行全部忽略：
   1. 注意：`_` 并不是一个空行，它是一个省略了标识符也省略了表达式的常量定义，这一点你需要清楚，不要大意。
3. 没有表达式的常量定义复用上一行的表达式：例如如下的代码，j中没有写表达式，那么j是复用上一行表达式的即`j = iota`，但此时iota的值会为2
   ```go
   const (
     i = iota
     j
   )
   ```
4. 从第一行开始，iota 从 0 逐行加一：也就是说赋值表达式里无论是否引用了 iota，也无论引用了多少次，iota 的都会从常量定义块的第一行（注意这里不计空行和注释）开始计数，从 0 开始，逐行加一，例如上面代码
   ```go
   const (
    i = 7   //iota = 1
    j       //iota = 2
    k       //iota = 3
  )

  const (
    a = 7   //iota = 1
    b       //iota = 2
    c = 3   //iota = 3
    m = 1 << iota   //iota = 4
    n       //iota = 5
    o       //iota = 6
  )
   ```
5. 替换所有 iota：直接将表达式中的iota全部替换为我们上一步写出的值即可
{% endnoteblock %}

<!-- more -->

## 拓展阅读
[彻底搞懂 golang 里的 iota](https://studygolang.com/articles/22468?fr=sidebar)