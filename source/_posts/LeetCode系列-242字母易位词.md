---
title: (LeetCode系列)242字母易位词
author: yirufeng
top: true
cover: true
toc: True
mathjax: false
summary: 字母易位词，指的是两个单词只是字母的排列顺序不同，换言之就是字母的种类数以及字母出现的次数都一致
categories: LeetCode
tags:
  - LeetCode
date: 2020-06-05 16:24:35
---
字母易位词，指的是两个单词只是字母的排列顺序不同，换言之就是**字母的种类数以及字母出现的次数都一致**

我们重点只需要验证 **字母出现的次数以及字母的种类数是否一致**

# 排序



> sorted() 不会改变传入可迭代对象的顺序，而是返回一个排好序的可迭代对象

```python
def isAnagram(self, s: str, t: str) -> bool:
  return sorted(s) == sorted(t)
```





# 哈希表



两种方案：

1. 使用两个哈希表 最后比较
2. 只使用一个哈希表，对第一个单词进行计数，之后对第2个单词计数的时候判断是否最后可以为0 或者中间如果有值小于0则退出



```python
    def isAnagram(self, s: str, t: str) -> bool:
        s_map, t_map = {}, {}
        for char_s in s:
            s_map[char_s] = s_map.get(char_s, 0) + 1
        for char_t in t:
            t_map[char_t] = t_map.get(char_t, 0) + 1
        return t_map == s_map

```





```python
    def isAnagram(self, s: str, t: str) -> bool:
        hashmap = {}
        for char_s in s:
            hashmap[char_s] = hashmap.get(char_s, 0) + 1
        for char_t in t:
            hashmap[char_t] = hashmap.get(char_t, 0) - 1
            if hashmap[char_t] < 0:
                return False
        return True
```





```python
    def isAnagram(self, s: str, t: str) -> bool:
        hashmap = {}
        for char_s in s:
            hashmap[char_s] = hashmap.get(char_s, 0) + 1
        for char_t in t:
            hashmap[char_t] = hashmap.get(char_t, 0) - 1
        for value in hashmap.values():
            if value != 0:
                return False
        return True
```



# 直接计数并返回

> 使用collections模块的Counter()



collections.Counter*类统计列表元素出现次数。定义 *Counter*(计数器)是对字典的补充,用于追踪值的出现次数



```python
    def isAnagram(self, s: str, t: str) -> bool:
        return collections.Counter(s) == collections.Counter(t)
```





# 总结：

了解字母异位词的概念：字母易位词，指的是两个单词只是字母的排列顺序不同，换言之就是**字母的种类数以及字母出现的次数都一致**



1. 使用sorted() **sorted() 不会改变传入可迭代对象的顺序，而是返回一个排好序的可迭代对象**
2. **使用collections.Counter()类统计列表元素出现次数。定义 Counter(计数器)是对字典的补充,用于追踪值的出现次数**
3. 不仅仅可以使用两个哈希表最后比较，**还可以使用1个哈希表**，判断是否有小于0的元素或者最后是否所有值都等于0




