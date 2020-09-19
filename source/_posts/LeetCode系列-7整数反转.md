---
title: (LeetCode系列)7整数反转
author: yirufeng
top: true
cover: true
toc: true
mathjax: false
summary: 对整数进行反转，我们需要熟记32位有符号整数的两个边界，需要注意的两个点：1. 如何处理负数 2. 需要考虑整数溢出的情况
categories: LeetCode
tags:
  - LeetCode
date: 2020-06-05 15:18:20
---



# 题意



仔细审题，两个关键点

1. **32位有符号整数，需要考虑负数如何处理**
2. 题中给的注意，**考虑是否溢出**



# 解法一：逆序累加

**我们将负数乘以-1，与正数统一处理，之后计算反转的结果之后再确认是否需要乘以-1**

`报错的代码`



```python
# 第一次提交：报错，错误测试用例，输入的内容经过反转之后超出了32位有符号整数的边界
    def reverse(self, x: int) -> int:
        num, ret = x if x >= 0 else (-1) * x, 0
        while num:
            ret, num = ret * 10 + num % 10, num // 10
        return ret if x >= 0 else (-1) * ret
```

**考虑边界情况之后的代码**

```
# 第二次提交：考虑边界情况，因此特意判断是否超出有符号整数的边界
    # 执行用时:36ms, 在所有Python3提交中击败了93.94 %的用户
    # 内存消耗:13.6MB, 在所有Python3提交中击败了6.67 % 的用户
    def reverse(self, x: int) -> int:
        # 正负数全部按照正数处理
        num, ret = x if x >= 0 else (-1) * x, 0
        while num:
            ret, num = ret * 10 + num % 10, num // 10
        # 判断是否溢出
        if x >= 0 and ret > 2147483647:
            return 0
        # 这里我们计算负数,但是我们最开始是将负数按照正数处理，所以反转之后的结果是正数，因为我们到最后才乘以-1
        elif x < 0 and ret > 2147483648:
            return 0
        return ret if x >= 0 else (-1) * ret
```





# 解法二：字符串反转



```
执行用时:48ms, 在所有Python3提交中击败了43.66 %的用户
内存消耗:13.7MB, 在所有Python3提交中击败了6.67 % 的用户
```

```python
    def reverse(self, x: int) -> int:
        num = x if x >= 0 else (-1 * x)
        num = int(str(num)[::-1]) if x >= 0 else -1 * int(str(num)[::-1])
        return 0 if num > 2147483647 or num < -2147483648 else num
```

# Java 解法 以及 注意事项 和边界情况的考虑 



![image-20200605152409130](https://gitee.com/yirufeng/images/raw/master/img/image-20200605152409130.png)

![image-20200605152459684](https://gitee.com/yirufeng/images/raw/master/img/image-20200605152459684.png)

[参考](https://leetcode.wang/leetCode-7-Reverse-Integer.html)

[参考](https://leetcode-cn.com/problems/reverse-integer/solution/javashi-pin-jiang-jie-xi-lie-reverse-integer-by-se/)





# 总结

记忆：`2^31-1=2147483647,-2^31=-2147483648`

> python中 取摸 取整除 需要注意 和java不一样

python 中 `-1%10 = 9` ， `-1//10=-1`
java   中 `-1%10 = 1` ， `-1//10=0`