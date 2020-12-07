---
title: (LeetCode系列)矩阵中的搜索
author: yirufeng
pin: false
toc: true
mathjax: false
categories:
  - 算法
  - 二分
keywords: LeetCode
tags:
  - 二分
  - 矩阵
date: 2020-12-07 17:47:08
---

## 套路总结

{% noteblock, 套路总结 %}

![sCRjwC](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/sCRjwC.png)

{% endnoteblock %}

{% note info, 注意：方法一二代码较为简单，这里就不再赘述了  %}

## 方法三
上图中的方法三，我们以[LeetCode 240](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/s)为例

{% folding green, 代码 %}
```go
/** ✅
 * @Author: yirufeng
 * @Date: 2020/12/7 11:41 上午
 * @Desc:
 * 方法一：暴力
 * 方法二：从右上角或者左下角每次排除一行或者一列
 * 方法三：每一行二分搜索
 * 方法四：采用二分
		思路：获取当前搜索矩阵的中心点，然后排出左上角或者右下角的矩阵
	如下是方法四代码
 **/
func searchTarget(matrix [][]int, x1, y1, x2, y2, target int) bool {
	//递归可能越界
	if x1 > x2 || y1 > y2 {
		return false
	}

	//之前这个if语句块没有写会死循环，
	//注意点：避免x1，y1，x2，y2相等时调用searchTarget(matrix, x1, y1, xMid, yMid, target)死循环
	if x1 == x2 && y1 == y2 {
		if matrix[x1][y1] == target {
			return true
		}
		return false
	}

	//得到中心点坐标
	xMid, yMid := x1+(x2-x1)>>1, y1+(y2-y1)>>1
	if matrix[xMid][yMid] > target { //排除右下角
		return searchTarget(matrix, x1, y1, xMid, yMid, target) ||
			searchTarget(matrix, x1, yMid+1, xMid-1, y2, target) ||
			searchTarget(matrix, xMid+1, y1, x2, yMid-1, target)
	} else if matrix[xMid][yMid] < target { //排除左上角
		return searchTarget(matrix, x1, yMid+1, xMid, y2, target) ||
			searchTarget(matrix, xMid+1, y1, x2, yMid, target) ||
			searchTarget(matrix, xMid+1, yMid+1, x2, y2, target)
	}
	return true

}

func searchMatrix(matrix [][]int, target int) bool {
	if len(matrix) == 0 || len(matrix[0]) == 0 {
		return false
	}
	return searchTarget(matrix, 0, 0, len(matrix)-1, len(matrix[0])-1, target)
}

```
{% endfolding %}

<!-- more -->


## 方法四

上图中的方法四，我们以牛客上的[NC86矩阵元素查找](https://www.nowcoder.com/practice/3afe6fabdb2c46ed98f06cfd9a20f2ce?tpId=117&&tqId=35008&rp=1&ru=/ta/job-code-high&qru=/ta/job-code-high/question-ranking)为例

{% folding green, 代码 %}
```go
package main

/**
 * 
 * @param mat int整型二维数组 
 * @param n int整型 
 * @param m int整型 
 * @param x int整型 
 * @return int整型一维数组
*/
func findElement(mat [][]int, n int, m int, x int) []int {
	// write code here


	//此时右上角对应的坐标
	curX, curY := 0, m-1
	for curX < n && curY > -1  {
		if mat[curX][curY] > x { //说明不在该列
			curY--
		} else if mat[curX][curY] < x { //说明不在该行
			curX++
		} else if mat[curX][curY] == x {
			return []int{curX, curY}
		}
	}

	//说明找不到
	return []int{-1, -1}
}
```
{% endfolding %}



## 第二种题型特有解法
上图中的针对第二种题型的解法，我们以[LeetCode74](https://leetcode-cn.com/problems/search-a-2d-matrix/)为例

> 题目中说明了m行n列，假设有序数组中的下标为i(0~m*n-1)，转换对应的矩阵所在位置为(i / n, i % n)

{% folding green, 代码 %}
```go
func searchMatrix(matrix [][]int, target int) bool {
	// write code here

	if len(matrix) == 0 || len(matrix[0]) == 0 {
		return false
	}
	row, col := len(matrix), len(matrix[0])
	l, r := 0, row*col-1
	for l <= r {
		mid := l + (r-l)>>1
		if matrix[mid/col][mid%col] > target { //说明
			r = mid - 1
		} else if matrix[mid/col][mid%col] < target {
			l = mid + 1
		} else if matrix[mid/col][mid%col] == target {
			return true
		}
	}

	//说明没找到
	return false
}
```
{% endfolding %}