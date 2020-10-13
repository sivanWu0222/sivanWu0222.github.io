---
title: golang中的defer-recover-panic
author: yirufeng
pin: true
toc: true
mathjax: false
categories:
  - go
tags:
  - go
  - defer
  - panic
  - recover
date: 2020-10-13 21:28:35
summary: golang中的错误处理
---
总结



## panic
{% note info, note info panic其实就是我们其他语言中的throw exception %}


## recover
{% note info, note info recover其实就是其他语言的try catch %}

{% noteblock, 注意 %}
1. recover如果想起作用的话， 必须在defered函数中使用。
2. 在正常函数执行过程中，调用recover没有任何作用, 他会返回nil。如这样：fmt.Println(recover()) 。
3. 如果当前的goroutine panic了，那么recover将会捕获这个panic的值，并且让程序正常执行下去。不会让程序crash。
{% endnoteblock %}



## 总结
{% noteblock, 总结 %}
1. 先执行return再执行defer
2. 如果触发多个panic，后触发的Panic会覆盖前面的Panic
3. defer-recover必须写在panic前面，因为发生panic后将不会执行位于panic后面的代码，如果在Panic之前有的defer代码将会按序执行
4. 如果当前函数触发了panic之后没有进行defer-recover，此时这个panic(会带着自己的异常去找对应的defer-recover)将会传回到上一级，如果上一级的defer链也没有对应的defer-recover将会一直返回到main，造成整个程序crash。**如果不想影响函数的主流程，需要在对应的函数panic前面加上defer-recover**
5. 协程遇到panic之后，将会遍历本协程的defer链表，并执行对应的defer，执行defer过程中，遇到recover就停止panic，返回recover处继续执行，如果没有遇到recover，遍历完本协程的defer链表，(将会沿着调用链返回上一级继续执行上一级的defer链表直到异常捕获或到main之后crash)或者如果没有上一级就会抛出错误信息，

{% endnoteblock %}

<!-- more -->


## defer 
1. defer会在return之后执行，因此可能会对return的值进行修改
2. defer会按照先入后出的顺序进行执行
{% folding green, 具体代码 %}
```go
package main

import "fmt"

/**
 * @Author: yirufeng
 * @Email: yirufeng@foxmail.com
 * @Date: 2020/10/13 9:08 下午
 * @Desc:
 */

func a() {
	i := 0
	defer fmt.Println(i)
	i++
	return
}

func b() {
	for i := 0; i < 4; i++ {
		defer fmt.Println(i)
	}
}

func f() (result int) {
	defer func() {
		result++
	}()
	return 0
}

func g() (r int) {
	t := 5
	defer func() {
		fmt.Println("t:", t)
		t = t + 5
	}()
	return t
}

func h() (r int) {
	defer func(r int) {
		r = r + 5
	}(r)
	return 1
}

func d(result int) {
	result = 0
	func() {
		result++
	}()
	return
}

func e() (r int) {
	t := 5
	r = t
	func() {
		t = t + 5
	}()
	return
}

func k() (r int) {
	r = 1
	func(r int) {
		r = r + 5
	}(r)
	return
}

func main() {
	a()
	fmt.Println("-------------------------")
	b()
	fmt.Println("-------------------------")
	fmt.Println(f())
	fmt.Println("-------------------------")

	fmt.Println(g())
	fmt.Println("-------------------------")

	fmt.Println(h())
	fmt.Println("-------------------------")

	d(2)
	fmt.Println("-------------------------")

	fmt.Println(e())
	fmt.Println("-------------------------")

	fmt.Println(k())
	fmt.Println("-------------------------")

}

```

执行结果：
```
0
-------------------------
3
2
1
0
-------------------------
1
-------------------------
t: 5
5
-------------------------
1
-------------------------
-------------------------
5
-------------------------
1
-------------------------
```
{% endfolding %}


## 参考文章列表
1. [5.4 panic 和 recover](https://draveness.me/golang/docs/part2-foundation/ch05-keyword/golang-panic-recover/)
2. [Dive into stack and defer/panic/recover in go](http://hustcat.github.io/dive-into-stack-defer-panic-recover-in-go/)
3. [Go官方](https://blog.golang.org/defer-panic-and-recover)
4. [Golang 入门系列（十四）defer, panic和recover用法](https://www.cnblogs.com/fengchuiyizh/p/12299611.htmls)
5. [理解Defer、Panic和Recover](https://www.cnblogs.com/sunshine-anycall/p/4746066.html)
6. [Go面试：从一道判断题来谈panic和defer的调用机制和执行顺序](https://blog.csdn.net/pengpengzhou/article/details/107663338)

