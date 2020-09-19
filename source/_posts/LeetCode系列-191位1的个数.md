---
title: (LeetCode系列)191位1的个数
author: yirufeng
top: true
cover: true
coverImg: /images/1.jpg
toc: true
mathjax: false
summary: 编写一个函数，输入是一个无符号整数，返回其二进制表达式中数字位数为 ‘1’ 的个数（也被称为汉明重量）。
categories: LeetCode
tags:
  - LeetCode
  - 位运算
date: 2020-05-26 14:24:19
---



## 解决方法

### 方法一：一位一位判断

> 我们对输入的数字从最后一位开始，每次和1进行与运算，之后每次左移1位

**为什么每次不右移1位呢？**

`因为如果对于有符号整数，每次右移我们前面会填充符号位(正数填0负数填1)因此对于后面的计数会有影响，而左移对于有符号整数都只是在右边填充0`



**区分逻辑右移，逻辑左移与算数右移，算数左移：**

1. 逻辑左移=算数左移，右边统一添0 

2. 逻辑右移，左边统一添0 

3. 算数右移，左边添加的数和符号有关



> 这里有一个前提条件，假定编译器指定Int为32位

```python
class Solution:
    # 方法一：前提条件。假设我们的无符号整数是32位
    def hammingWeight(self, n: int) -> int:
        num = 1
        count = 0
        for i in range(32):  # 是否为32取决于编译器的类型
            if num & n:
                count += 1
            num <<= 1
        return count
```







时间复杂度：o(二进制的位数)

空间复杂度：o(1)



### 方法二：使用n & (n-1) 直接判断最后1个1，从而每次进行计数

时间复杂度：o(1的个数)

空间复杂度：o(1)



```python
class Solution:
    # 方法二：
    def hammingWeight(self, n: int) -> int:
        count = 0
        while n:
            n = n & (n - 1)
            count += 1
        return count
```





### 方法三：使用python自带的内置函数

两个预备知识：

1. bin(n) 以字符串的形式返回n的二进制表达
2. count() 用于统计字符串里某个字符出现的次数

```python
class Solution:

    # 方法三：
    def hammingWeight(self, n: int) -> int:
        # bin(n) 以字符串的形式返回n的二进制表达
        # count() 用于统计字符串里某个字符出现的次数
        return bin(n).count('1')
```





