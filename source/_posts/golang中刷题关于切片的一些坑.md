---
title: Golang中刷题关于切片的一些坑
author: yirufeng
pin: true
toc: true
mathjax: false
categories:
  - 面试
  - 算法
tags:
  - 刷题
  - 算法
  - Golang
date: 2020-11-18 09:58:06
---

## 问题1

问题描述：递归的过程中，传递了一个切片作为参数，每次我们都需要对切片切取首元素，之后递归回来的时候发现切片中的元素还是没有切取首元素，**是一个很严重的逻辑错误**。

**问题解决：我们递归函数使用切片作为参数的时候，我们直接传入切片的地址。因为切片每次删除元素，地址也会改变。当切片增加元素引起扩容，地址也会发生改变，而如果只传入切片我们只是引用最开始的时候的切片的地址，因此会发现切片没有任何改变。**

例如我们自己写的二叉树序列化和反序列化代码：在最开始只是传递一个切片，但是后来传递切片的地址进去发现代码逻辑正常运行。

{% folding green, 代码 %}

```go
//方法一：使用先序遍历进行序列化和反序列化
//进行序列化
func PreorderSerialize(root *TreeNode) string {
	if root == nil {
		return "#!"
	}

	var ret string

	ret += strconv.Itoa(root.Val) + "!"
	ret += PreorderSerialize(root.Left)
	ret += PreorderSerialize(root.Right)
	return ret
}

//进行反序列化
func PreorderDeserialize(ret string) *TreeNode {
	strs := strings.Split(ret, "!")

	//注意点：由于切片在执行过程中有可能会因为增加和删除元素而造成切片不是原来那个切片，但是我们递归回去的时候还是指向原来的切片，因此会有问题
	//所以这里我们传递的是切片的地址
	//因为切片扩容可能会生成一个新的底层数组，并且由于切片移除了元素，因此对应的头部地址一定会改变，所以会造成地址的改变
	root := ReconstructTreeFromPreorder(&strs)
	return root
}

//根据我们分割后的字符串建立二叉树
func ReconstructTreeFromPreorder(strs *[]string) *TreeNode {
	if (*strs)[0] == "#" {
		(*strs) = (*strs)[1:]
		return nil
	}

	//首先将该值对应的字符串转换为int
	val, _ := strconv.Atoi((*strs)[0])
	//建立一个针对于该值的节点
	node := &TreeNode{
		Val: val,
	}

	//去掉我们建立过的节点的值
	(*strs) = (*strs)[1:]
	//之后进行递归建立左右子树
	node.Left = ReconstructTreeFromPreorder(strs)
	node.Right = ReconstructTreeFromPreorder(strs)

	return node
}

```

{% endfolding %}

<!-- more -->