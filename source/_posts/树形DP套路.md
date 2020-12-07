---
title: 树形DP套路
author: yirufeng
pin: false
toc: true
mathjax: false
categories: 
  - 算法
  - 面试
tags:
  - DP
  - 树
  - 树形DP
date: 2020-11-17 14:12:50
---

## 步骤

1. 以某个节点X为头结点的子树中，分析答案有哪些可能性，并且这种分析是以X的左子树，X的右子树，和X整棵树的角度来考虑可能性的
2. 根据第一步分析的可能性，列出所需要的信息，例如在二叉树是否平衡中，左子树和右子树都需要知道各自是否平衡，以及高度这两个信息。
3. 根据第二步信息汇总，定义自己的信息结构体
4. 设计递归函数，递归函数是处理以X为头结点的情况相爱的答案，包括设计递归函数的base case，默认直接得到左树和右树所有的信息，以及把可能性左整合，并且返回第三步的信息结构这4个小步骤。


<!-- more -->


## 题目应用：判断一颗二叉树是否平衡

### 第一步：

>  内容：考虑可能性

​	可能性一：如果X的左子树不平衡，说明以X为头结点的树就是不平衡的

​	可能性二：如果X的右子树不平衡，说明以X为头结点的树就是不平衡的

​	可能性三：如果X的左右子树高度差超过1，说明以X为头结点的树就是不平衡的

​	可能性四：如果前三种可能性都不满足，说明以X为头结点的树是平衡的。

**注意：空树也是一个平衡二叉树，会满足可能性四**

### 第二步：

> 内容：根据第一步的可能性分析，列出所需要的信息。这里左右子树都需要知道是否平衡以及高度这两个信息。

### 第三步：

> 内容：汇总信息，并为此定义一个信息结构体

### 第四步：

> 内容：设计递归函数，递归函数是处理以X为头结点的情况下的答案，包括设计递归函数的base case，默认直接得到左右子树的信息，以及把可能性做整合并且也返回第三步的信息结构这4个小步骤。


{% folding green, 代码 %}

```go
package main

import "leetcode-go/model"	//model中只定义了一个TreeNode

type RetType struct {
	IsBalanced bool
	Height     int
}

func isBalanced(root *model.TreeNode) bool {
	return isBalancedCore(root).IsBalanced
}

func isBalancedCore(root *model.TreeNode) RetType {
	if root == nil {
		return RetType{
			IsBalanced: true,
			Height:     0,
		}
	}

	//获取左右子树的信息
	left := isBalancedCore(root.Left)
	right := isBalancedCore(root.Right)

	//如果左右子树都平衡且左右子树高度差绝对值小于等于1，直接返回True信息
	if left.IsBalanced == true && right.IsBalanced == true && left.Height-right.Height >= -1 && left.Height-right.Height <= 1 {
		return RetType{
			IsBalanced: true,
			Height:     max(left.Height, right.Height) + 1,
		}
	}

	//否则说明不平衡直接返回false
	return RetType{
		IsBalanced: false,
		Height:     max(left.Height, right.Height) + 1,
	}
}

func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}

```



{% endfolding %}