---
title: go中的heap实现大根堆与小根堆
author: yirufeng
top: true
toc: true
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
1. 我们使用Less比较的时候决定了大根堆或小根堆
2. push以及pop需要传入指针，因为涉及到改动。
3. pop的时候需要删除最后一个，因为我们是append自己模拟进去的
4. 我们自己用堆加入元素的时候需要使用heap中的方法来加入元素`heap.push()`和删除元素`heap.pop()`
5. 如果我们自己想要查看堆顶，切片中索引为0对应的元素就是堆顶

```go
minHeap := &MinHeap{}
heap.Init(minHeap)
heap.Push(minHeap, 123)
heap.Push(minHeap, 999)
num := heap.Pop(minHeap)  //将会弹出堆顶
```


<!-- more -->


```go
type MinHeap []int

//*************************************小根堆
func (h MinHeap) Len() int {
	return len(h)
}

//小根堆，前面比后面小
func (h MinHeap) Less(i, j int) bool {
	return h[i] < h[j]
}

func (h MinHeap) Swap(i, j int) {
	h[i], h[j] = h[j], h[i]
}

func (h *MinHeap) Push(x interface{}) {
	*h = append(*h, x.(int))
}

func (h *MinHeap) Pop() interface{} {
	val := (*h)[len(*h)-1]
	*h = (*h)[:len(*h)-1]
	return val
}
```

## 大根堆

> 同小根堆，改变Less实现方法即可

```go
type MaxHeap []int


//********************************************大根堆
func (h MaxHeap) Len() int {
	return len(h)
}

//大根堆，前面比后面大
func (h MaxHeap) Less(i, j int) bool {
	return h[i] > h[j]
}

func (h MaxHeap) Swap(i, j int) {
	h[i], h[j] = h[j], h[i]
}

func (h *MaxHeap) Push(x interface{}) {
	*h = append(*h, x.(int))
}

func (h *MaxHeap) Pop() interface{} {
	val := (*h)[len(*h)-1]
	*h = (*h)[:len(*h)-1]
	return val
}
```