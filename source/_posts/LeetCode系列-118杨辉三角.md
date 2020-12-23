---
title: (LeetCode系列)118杨辉三角
author: yirufeng
top: true
cover: true
coverImg: /images/1.jpg
toc: True
mathjax: false
summary: 给定一个非负整数 numRows，生成杨辉三角的前 numRows 行。在杨辉三角中，每个数是它左上方和右上方的数的和。
sidebar: [blogger, webinfo, tagcloud, category]
categories: 
    - LeetCode
    - dp
tags:
  - LeetCode
  - dp
date: 2020-05-26 15:16:14
---



## 解法



### 解法一(自己的解法，蛮力求解，找关系)

发现每一行如果大于2个元素两边都用1填充，并且其他位置的元素的关系式如下：

1. `ele[i][j] = ele[i-1][j-1] + ele[i-1][j]`

2. `ele[i][j] = 1 当且仅当j=0或j=i时`



```python
class Solution:
    def generate(self, numRows: int):
        # 规律第i行有i个元素
        li = []
        for i in range(numRows):
            li_li = []
            # 从1开始到第i+1结束
            for j in range(0, i+1):
                print(i, j)
                if j == 0 or j == i:
                    li_li.append(1)
                else:
                    # 递推公式
                    li_li.append(li[i-1][j-1] + li[i-1][j])
            li.append(li_li)
        return li
```





### 解法二：(LeetCode他人解法)

观察一下规律，发现当前一行只比上一行多了一个元素，最最关键的一点：本行元素等于上一行元素往后错一位再逐个相加：[参考](https://leetcode-cn.com/problems/pascals-triangle/solution/qu-qiao-jie-fa-cuo-yi-wei-zai-zhu-ge-xiang-jia-28m/)

```python
class Solution:
    def generate(self, numRows: int) -> List[List[int]]:
        if numRows == 0: return []
        res = [[1]]
        while len(res) < numRows:
            newRow = [a+b for a, b in zip([0]+res[-1], res[-1]+[0])]
            res.append(newRow)      
        return res
```



### 解法三：官方解法[参考](https://leetcode-cn.com/problems/pascals-triangle/solution/yang-hui-san-jiao-by-leetcode/)

> 官方使用了None 进行占位，之后再具体对None改为具体的值



```python
class Solution:
    def generate(self, num_rows):
        triangle = []

        for row_num in range(num_rows):
            # The first and last row elements are always 1.
            row = [None for _ in range(row_num+1)]
            row[0], row[-1] = 1, 1

            # Each triangle element is equal to the sum of the elements
            # above-and-to-the-left and above-and-to-the-right.
            for j in range(1, len(row)-1):
                row[j] = triangle[row_num-1][j-1] + triangle[row_num-1][j]

            triangle.append(row)

        return triangle
```

