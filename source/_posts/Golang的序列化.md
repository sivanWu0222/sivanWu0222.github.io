---
title: golang的序列化
author: yirufeng
pin: false
toc: true
mathjax: false
summary: 
sidebar: [blogger, webinfo, tagcloud, category]
categories:
  - go
  - json
tags:
  - go
  - 序列化
date: 2020-10-05 20:46:31
---

{% noteblock info %}
1. Go中使用encoding/json中的Marshal以及Unmarshal对数据进行序列化和反序列化
2. json中的key永远为字符串，Golang中一般我们键都为string，value为interface，之后拿到interface采用反射来判断类型，
3. 序列化的时候首字母必须大写，不然访问不到
4. 如果希望序列化之后结构体的key有其他别名或者首字母小写，可以加上一个tag
{% endnoteblock %}

{% folding cyan open,  具体的序列化 %}
1. map序列化之后，Key为json中的key,value也是json中的value
2. 结构体序列化之后，如果有tag，那么key就是对应的tag
{% endfolding %}

{% noteblock info %}
注意：使用Unmarshal()的时候，第2个参数，直接传声明的变量进去即可，因为Golang会自动帮我们分配内存和赋值
**例如传入一个只声明的map或结构体**
{% endnoteblock %}

<!-- more -->

## 具体实现

{% folding cyan open,  主要代码 %}
```go
package main

import (
	"encoding/json"
	"fmt"
)

/**
 * @Author: yirufeng
 * @Email: yirufeng@foxmail.com
 * @Date: 2020/10/5 11:49 上午
 * @Desc: 序列化数据与数据的反序列化
 */

type Book struct {
	Id    string `json:"book_id"`
	Title string `json:"book_name"`
	Press string `json:"book_press"`
}

type Student struct {
	Name      string
	Age       int
	Gender    bool
	ClassInfo map[int][]string //学生所属的班级信息
}

func MarshalMap() ([]byte, error) {
	mymap := make(map[int]string)
	mymap[110] = "警察"
	mymap[120] = "救护"
	data, err := json.Marshal(mymap)
	if err != nil {
		fmt.Println("序列化结构体失败----------------------")
		return nil, err
	}
	return data, nil
}

func MarshalTagStruct() ([]byte, error) {
	book := &Book{
		Id:    "12333",
		Title: "鲁滨孙漂流记",
		Press: "电子信息出版社",
	}
	data, err := json.Marshal(book)
	if err != nil {
		fmt.Println("序列化结构体失败----------------------")
		return nil, err
	}
	fmt.Println(string(data))
	return data, err
}

func MarshalUnTagStruct() ([]byte, error) {

	classInfo := make(map[int][]string)

	classInfo[1501] = []string{"理论", "时间"}
	//定义一个学生
	stu := &Student{
		Name:      "yirufeng",
		Age:       22,
		Gender:    true,
		ClassInfo: classInfo,
	}

	data, err := json.Marshal(stu)
	if err != nil {
		fmt.Println("序列化结构体失败----------------------")
		return nil, err

	}
	fmt.Println(string(data))
	return data, nil
}

func MarshalInt() {
	num := 10000
	data, err := json.Marshal(num)
	if err != nil {
		fmt.Println("序列化int失败----------------------")
		return
	}
	fmt.Println(string(data))
}

func MarshalSlice() {
	name := []string{"231321", "3213", "412"}
	data, err := json.Marshal(name)
	if err != nil {
		fmt.Println("序列化切片失败----------------------")
		return
	}
	fmt.Println(string(data))
}

func UnmarshalMap(content []byte) {
	stuMap := make(map[int]string)

	err := json.Unmarshal(content, &stuMap)
	if err != nil {
		fmt.Println("反序列化map失败----------------------", err)
		return
	}
	fmt.Println("反序列化map成功-----------")
	fmt.Println(stuMap)
}

func UnmarshalStruct(data interface{}) {
	var ret Student
	err := json.Unmarshal(data.([]byte), &ret)
	if err != nil {
		fmt.Println("反序列化结构体失败----------------------", err)
		return
	}
	fmt.Println("反序列化结构体成功-----------")
	fmt.Println(ret)
}

func main() {
	MarshalInt()
	MarshalSlice()
	MarshalTagStruct()
	data, err := MarshalMap()
	if err != nil {
		fmt.Println("序列化map失败----------------------")
		return
	}
	UnmarshalMap(data)

	data2, err := MarshalUnTagStruct()
	if err != nil {
		fmt.Println("序列化结构体失败----------------------")
		return
	}
	UnmarshalStruct(data2)
}

```
{% endfolding %}
