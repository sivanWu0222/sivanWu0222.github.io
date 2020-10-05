---
title: Golang中的错误处理
author: yirufeng
pin: false
toc: true
mathjax: false
summary: Golang中的错误处理
categories:
  - go
  - 错误
tags:
  - go
  - 错误
  - error
date: 2020-10-05 20:46:39
---

## 自定义错误
{% noteblock info %}
1. 因为error是一个接口，因此我们可以自定义一个error
2. 实际开发中我们要判断错误的类型，直接进行类型断言（也可以通过switch进行类型断言分支）
{% endnoteblock %}

{% folding cyan open,  三部曲自定义一个错误 %}
1. 自定义一个错误结构体
2. 实现我们的Error方法
3. 返回错误的时候直接返回一个`&我们自己的错误`
{% endfolding %}

{% noteblock info %}
注意：我们最常用的就是`errors.New(字符串)`
{% endnoteblock %}

<!-- more -->

## 具体实现

{% folding cyan open,  主要代码 %}
```go
package main

import (
	"fmt"
)

/**
 * @Author: yirufeng
 * @Email: yirufeng@foxmail.com
 * @Date: 2020/10/5 12:10 下午
 * @Desc: 错误处理，3部曲，自己已经罗列在下方了
背景：因为error是一个interface，因此我们可以自定义错误，并进行返回

另外一种返回错误的方法，比较常用：`return errors.New("这是一个简单的错误")`
*/

//自定义一个错误
//1. 自定义一个错误结构体
type FileError struct {
	Path   string //记录文件所在路径
	Err    string //记录文件名
	OpType string //记录对文件的什么操作
}

//2. 实现Error方法
func (f *FileError) Error() string {

	return fmt.Sprintf("%s %s %s", f.Path, f.Err, f.OpType)
}

//3. 返回的时候直接返回我们错误结构体的引用
func fail() error {
	//另外一种返回错误的方法，比较常用
	//return errors.New("这是一个简单的错误")
	return &FileError{
		Path:   "./",
		Err:    "无法打开文件",
		OpType: "read",
	}
}

func main() {
	err := fail()
	if err != nil {
		fmt.Println(err)
	}

	fmt.Println()
}
```
{% endfolding %}
