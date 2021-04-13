---
title: go中的heap实现大根堆与小根堆
author: yirufeng
toc: true
sidebar: [blogger, webinfo, tagcloud, category]
categories: 
	- 面试
	- 算法
tags:
  - go
  - 算法
  - 面试
  - 堆
date: 2020-09-12 09:10:00
---
## 小根堆
> 几个注意点：
1. 因为heap需要我们实现sort接口(3个方法，Less，Swap，以及Len)我们使用Less比较的时候决定了大根堆或小根堆
2. push以及pop因为涉及到切片中元素个数的改变，可能进行扩容或缩容，所以要使用指针
3. Push以及Pop的实现就按照对切片的处理即可
4. 之后初始化我们的堆的时候需要先建立我们自己的堆，然后使用`heap.Init()`初始化
4. 后面堆要弹出或加入元素都必须使用`heap.Push()`以及`heap.Pop()`


<!-- more -->


```go
package main

import (
	"container/heap"
	"fmt"
)

/**
 * @Author: yirufeng
 * @Date: 2021/4/13 3:23 下午
 * @Desc:
 **/

type MinHeap []int

func (h *MinHeap) Push(val interface{}) {
	*h = append(*h, val.(int))
}

func (h *MinHeap) Pop() interface{} {
	val := (*h)[len(*h)-1]
	*h = (*h)[:len(*h)-1]
	return val
}

// Less 实现sort接口的三个方法
func (h MinHeap) Less(i, j int) bool {
	return h[i] < h[j]
}

func (h MinHeap) Swap(i, j int) {
	h[i], h[j] = h[j], h[i]
}

func (h MinHeap) Len() int {
	return len(h)
}

func main() {

	//新建一个小顶堆
	h := &MinHeap{}
	//初始化我们的小顶堆
	heap.Init(h)

	//之后我们使用Push以及Pop的时候都必须使用heap包自带的方法
	heap.Push(h, 99)
	heap.Push(h, 123)
	heap.Push(h, 13)
	heap.Push(h, 78)
	heap.Push(h, 900)
	heap.Push(h, 1314)
	heap.Push(h, 213)
	heap.Push(h, 31)

	for h.Len() > 0 {
		fmt.Printf("%d ", heap.Pop(h))
	}

}
```

## 大根堆

> 同小根堆，改变Less实现方法即可

```go
package main

import (
	"container/heap"
	"fmt"
)

/**
 * @Author: yirufeng
 * @Date: 2021/4/13 2:45 下午
 * @Desc:
 **/

type MaxHeap []int

func (h *MaxHeap) Push(x interface{}) {
	*h = append(*h, x.(int))
}

func (h *MaxHeap) Pop() interface{} {
	ret := (*h)[len(*h)-1]
	*h = (*h)[:len(*h)-1]
	return ret
}

func (h MaxHeap) Len() int {
	return len(h)
}

func (h MaxHeap) Swap(i, j int) {
	h[i], h[j] = h[j], h[i]
}

func (h MaxHeap) Less(i, j int) bool {
	//按照升序排列
	return h[i] > h[j]
}

func main() {
	//新建一个大顶堆
	h := &MaxHeap{}
	//初始化为大顶堆
	heap.Init(h)

	//之后每次插入和删除元素的时候我们都必须使用heap.Push或者 heap.Pop方法来进行元素的插入和删除
	heap.Push(h, 1)
	heap.Push(h, 99)
	heap.Push(h, 33)
	heap.Push(h, 12)
	heap.Push(h, 108)
	for h.Len() > 0 {
		fmt.Printf("%d ", heap.Pop(h))
	}
}

```