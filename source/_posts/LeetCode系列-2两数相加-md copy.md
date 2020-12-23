---
title: (LeetCode系列)刷题记
author: yirufeng
top: true
cover: true
toc: true
mathjax: false
summary: 通过文档记录自己的LeetCode刷题记录
sidebar: [blogger, webinfo, tagcloud, category]
categories: 
    - LeetCode
tags:
  - LeetCode
date: 2020-06-05 11:08:18
---



# 两数相加



思路：

1. 分别求出两个链表都有数字时各个数字的和，
   1. 如果产生进位，将进位记录在计算下一位的时候加上
   2. 如果没有产生进位，移动到下一位
2. 如果有链表为空，那么直接计算剩余链表的数字 与 进位的和
3. 直到最后两个链表都为空，但是还有进位，此时我们还要再给进位分配一个链表的节点



## 求公共链表之后单独求不为空的链表的解法

```python
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        dummy_node = head = ListNode(0)
        addOne = 0
        while l1 and l2:
            sum = l1.val + l2.val + addOne
            head.next = ListNode(sum % 10)
            addOne, head, l1, l2 = sum // 10, head.next, l1.next, l2.next
        l3 = l1 if l1 else l2
        while l3:
            sum = l3.val + addOne
            head.next = ListNode(sum % 10)
            addOne, head, l3 = sum // 10, head.next, l3.next
        # 如果遍历完但是最后还有一个进位
        head.next = ListNode(addOne) if addOne else None
        return dummy_node.next
```





## 原始的解法

```python
class Solution:
    # 最开始的写法：超时
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        # 设置一个dummy node用来表示链表的哨兵头
        dummy_node = ListNode(0)
        head = dummy_node
        addOne = 0  # 用来表示进位
        # 循环结束条件
        while l1 or l2 or addOne:
            # 判断l1或l2是否为0
            val1 = l1.val if l1 else 0
            val2 = l2.val if l2 else 0
            sum = val1 + val2 + addOne
            head.next = ListNode(sum % 10)
            addOne = 1 if sum >= 10 else 0
            # 移动的时候也需要判断是否为空
            # if l1.next:
            if l1:  # 这里不能判断l1.next,因为我们的大循环下面的l1可能为空
                l1 = l1.next
            # if l2.next:
            if l2:
                l2 = l2.next
            head = head.next
        return dummy_node.next
```



## 原始解法(改进)

> 
> 改进: 使用三元运算符比单纯的if或者if-else效率高



```python
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        # 设置一个dummy node用来表示链表的哨兵头
        head = dummy_node = ListNode(0)
        addOne = 0  # 用来表示进位
        # 循环结束条件
        while l1 or l2 or addOne:
            # 判断l1或l2是否为0
            sum = (l1.val if l1 else 0) + (l2.val if l2 else 0) + addOne
            head.next = ListNode(sum % 10)
            head = head.next
            addOne = sum // 10
            # 移动的时候也需要判断是否为空
            l1 = l1.next if l1 else None
            l2 = l2.next if l2 else None
        return dummy_node.next

```



## 递归

```python
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        head = dummy_node = ListNode(0)

        def AddNum(l1: ListNode, l2: ListNode, extra: int, head: ListNode) -> None:
            # 递归结束条件
            if not l1 and not l2 and not extra:
                return
            sum = (l1.val if l1 else 0) + (l2.val if l2 else 0) + extra
            head.next = ListNode(sum % 10)
            head, l1, l2, extra = head.next, (l1.next if l1 else None), (l2.next if l2 else None), sum // 10
            AddNum(l1, l2, extra, head)

        AddNum(l1, l2, 0, head)

        return dummy_node.next
```

