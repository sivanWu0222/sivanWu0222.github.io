---
title: golang刷回溯遇到的坑
author: yirufeng
top: false
cover: false
coverImg: /images/1.jpg
toc: true
mathjax: false
sidebar: [blogger, webinfo, tagcloud, category]
categories: 
	- LeetCode
	- 回溯
keywords: 刷题
tags:
  - LeetCode
  - Golang
date: 2020-09-06 18:55:42
---


## 情况说明

今天在做LeetCode-46题，DFS深搜写完，最后结果愣是不对，中途调试发现每次满足结束递归条件时得到的结果都是对的，但是后面一有满足条件就会造成最终的结果不对。

下面是46题的错误代码，中途调试打印cur可以正确打印，但是打印ret每次都会改变

```go
func permute(nums []int) [][]int {
	if len(nums) == 0 {
		return nil
	}
	used, cur, res := make([]bool, len(nums)), []int{}, [][]int{}

	generatePermutation(&nums, &res, &used, cur)
	return res
}

func generatePermutation(nums *[]int, res *[][]int, used *[]bool, cur []int) {

	if len(*nums) == len(cur) {
		//just for test
		fmt.Printf("添加元素------>")
		for _, val := range cur {
			fmt.Printf("%v\t", val)
		}
		fmt.Println(cur)
		temp := make([]int, len(*nums))
		copy(temp, cur)
		*res = append(*res, temp)
		//fmt.Println(res)
	}

	//添加正确之后的对比
	//if len(*nums) == len(cur) {
	//	ans := make([]int, len(cur))
	//	copy(ans, cur)
	//	*res = append(*res, ans)
	//	return
	//}

	for i := 0; i < len(*nums); i++ {
		//如果当前值没有使用
		if !(*used)[i] {
			(*used)[i] = true
			cur = append(cur, (*nums)[i])
			//进行递归
			generatePermutation(nums, res, used, cur)
			//到这里说明之前的已经遍历完了
			(*used)[i] = false
			cur = cur[:len(cur)-1]
		}
	}

}
```
## 原因：

golang中二维切片中，第1个维度都是直接引用你加入结果时的那个切片(回溯中大多都是满足结束条件我们就加入，因此长度都一致的)，因此一旦这个切片后面修改，这个引用的结果也会改变，从而造成中间结果正确，最终结果错误。

## 修改

以后遇到能够正确输出的情况一定要先将结果copy出来放入最终我们要的结果列表中

正确代码
```go

func permute(nums []int) [][]int {
	if len(nums) == 0 {
		return nil
	}
	used, cur, res := make([]bool, len(nums)), []int{}, [][]int{}

	generatePermutation(&nums, &res, &used, cur)
	return res
}

func generatePermutation(nums *[]int, res *[][]int, used *[]bool, cur []int) {
	//just for test
	//if len(*nums) == len(cur) {
	//	fmt.Printf("添加元素------>")
	//	for _, val := range cur {
	//		fmt.Printf("%v\t", val)
	//	}
	//	fmt.Println(cur)
	//	temp := make([]int, len(*nums))
	//	copy(temp, cur)
	//	*res = append(*res, temp)
	//	//fmt.Println(res)
	//}

	//添加正确之后的对比
	if len(*nums) == len(cur) {
		temp := make([]int, len(cur))
		copy(temp, cur)
		*res = append(*res, temp)
		return
	}

	for i := 0; i < len(*nums); i++ {
		//如果当前值没有使用
		if !(*used)[i] {
			(*used)[i] = true
			cur = append(cur, (*nums)[i])
			//进行递归
			generatePermutation(nums, res, used, cur)
			//到这里说明之前的已经遍历完了
			(*used)[i] = false
			cur = cur[:len(cur)-1]
		}
	}

}
```
