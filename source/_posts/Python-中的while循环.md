---
title: Python 中的while循环
author: yirufeng
top: false
cover: false
coverImg: /images/1.jpg
toc: true
mathjax: false
summary: python 中常见的while循环 以及 python与其他语言不同的while-else循环执行流程,尤其要注意else的执行时机
categories: 
  - python
tags:
  - python
date: 2020-06-08 09:20:05
---



# while

> python除了正常的while 循环，还可以在while循环后带上一个else分支，那么执行流程是怎么样的呢？



话不多说，上来三段代码：



- ```python
  # while 循环如果被打断，则不执行else
  def func():
      count = 1
      while count < 5:
          print(count)
          count += 1
          return
      else:
          print(666)
  
  
  if __name__ == '__main__':
      func()
  ```

打印结果：
```shell
1
```




- ```python
  count = 1
  while count < 5:
      print(count)
      count += 1
  else:
      print("last")
  ```



打印结果：

```shell
1
2
3
4
last
```





- ```python
  count = 1
  while count < 5:
      print(count)
      count += 1
      break
  else:
      print("last")
  ```

打印结果：

```shell
1
```







分析原因：**python执行while 循环，当while循环条件不满足将会执行else语句，但是如果在while循环执行的过程中遇到了break 或者 return 语句 将不会执行else分支**

