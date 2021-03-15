---
title: 基本数据结构-slice
author: yirufeng
pin: true
toc: true
mathjax: false
sidebar:
  - blogger
  - webinfo
  - tagcloud
  - category
categories:
  - go
  - 基本数据结构
tags:
  - go的基本数据结构
date: 2021-02-10 09:29:35
summary:
---

## 查看slice结构

{% note info, 查看slice内部的构造：在runtime/slice.go中查看源码 %}

```go
// runtime/slice.go
type slice struct {
    array unsafe.Pointer // 元素指针
    len   int // 长度 
    cap   int // 容量
}
```

{% noteblock, 结构剖析，slice 共有三个属性： %}

1. 指针，指向底层数组，同一个底层数组可以被多个slice同时指向，因此只要有一个slice修改，其他指向该底层数组的slice也会被修改； 
2. 长度，表示切片可用元素的个数，也就是说**使用下标对 slice 的元素进行访问时，下标不能超过 slice 的长度**；
3. 容量，底层数组的元素个数，容量 >= 长度。**在底层数组不进行扩容的情况下，容量也是 slice 可以扩张的最大限度**。

{% endnoteblock %}

对切片进行赋值的时候，实际是对表层结构体的拷贝，但是拷贝出来的结构体与原来的结构体的array指向同一个底层数组。

## 切片的三种状态

{% note info, 平常我们创建切片的方式有很多种，其实里面就包含了我们切片中的三种状态（零切片，nil切片，空切片），只是很少有人关注罢了 %}

|             |                  创建方式一                   |              创建方式二              |                     特点                     |
| :---------: | :-------------------------------------------: | :----------------------------------: | :------------------------------------------: |
| zero slice  | 【任意数据类型都可以】`s1 := make([]int, 10)` | 【指针类型】`s2 := make([]*int, 10)` |       数组中的元素值都是对应类型的零值       |
|  nil slice  |                `var s1 []int`                 |         `s1 := *new([]int)`          |                底层数组是nil                 |
| empty slice |            `s1 := make([]int, 0) `            |           `s1 := []int{}`            | 底层数组都指向某一个特殊的内存地址(zerobase) |
<!-- more -->
{% noteblock, nil切片与我们的空切片的区别： %}

1. nil 切片和空切片很相似，长度和容量都是0，官方建议尽量使用 nil 切片，看原因3。

2. 创建出来的nil slice，它的长度和容量都为0。指向数组的指针也是0。这里比较混淆的是empty slice，empty slice的长度和容量也都为0，但是所有的空切片的数据指针都指向同一个地址 0xc42003bda0(zerobase)。空切片和 nil slice 比较的结果为false。

3. **nil切片与nil比较为true，空切片与nil比较为false**

4. 序列化之后的结果，**nil切片json序列化之后为`null`，但是空切片序列化之后为`[]`也就是我们json中的空数组**

5. **结构体中有一个切片属性，我们创建结构体的时候没有给切片属性赋值默认是nil切片，如果赋值`[]int{}`则为空切片。**如下案例中，c1的students就是一个empty slice，但是c2的students是一个nil slice

   ```go
   type class struct {
   	students []int
   }
   
   func main() {
   	c1 := class{
   		students: []int{},
   	}
   	c2 := class{
   	}
   }
   ```

{% endnoteblock %}


## 截取

> 语法：data[low:high:max] 要求low <= high <= max，此时长度为high-low，容量为max-low。也就是长度不包括high指向的元素，容量不包括max指向的元素。

1. `s1 := slice[2:5]` 表示s1是从slice的2号索引开始，目前的最后一个元素是位于slice的4号索引，也就是说长度位于5号索引位置，容量没有给出的话默认是slice结束
2. `s2 := s1[2:6:7]` 表示s2是从s1的2号索引开始，目前的最后一个元素位于s1的5号索引，也就是说长度位于6号索引位置(不包括6号位置)，容量是位于7号索引(不包括7号位置)


如图所示：![XPEifC](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/XPEifC.png)

### 案例：以下结果输出什么？
```go
func main() {
	
	slice := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9}
	s1 := slice[2:5]
	s2 := s1[2:6:7]

	s2 = append(s2, 100)
	s2 = append(s2, 200)

	s1[2] = 20

	fmt.Println(s1)
	fmt.Println(s2)
	fmt.Println(slice)
}
```

输出结果：
```
[2 3 20]
[4 5 6 7 100 200]
[0 1 2 3 20 5 6 7 100 9]
```

## 扩容
> 使用append添加元素，如果容量不够将会进行扩容，调用的是`growslice`函数
> 传入的三个参数分别是：老的元素的类型，老的切片，最小求得的容量
{% noteblock, golang 1.15.5 的 runtime/slice.go 中的growslice函数 %}
```go
// growslice handles slice growth during append.
// It is passed the slice element type, the old slice, and the desired new minimum capacity,
// and it returns a new slice with at least that capacity, with the old data
// copied into it.
// The new slice's length is set to the old slice's length,
// NOT to the new requested capacity.
// This is for codegen convenience. The old slice's length is used immediately
// to calculate where to write new values during an append.
// TODO: When the old backend is gone, reconsider this decision.
// The SSA backend might prefer the new length or to return only ptr/cap and save stack space.
func growslice(et *_type, old slice, cap int) slice {
	if raceenabled {
		callerpc := getcallerpc()
		racereadrangepc(old.array, uintptr(old.len*int(et.size)), callerpc, funcPC(growslice))
	}
	if msanenabled {
		msanread(old.array, uintptr(old.len*int(et.size)))
	}

	if cap < old.cap {
		panic(errorString("growslice: cap out of range"))
	}

	if et.size == 0 {
		// append should not create a slice with nil pointer but non-zero len.
		// We assume that append doesn't need to preserve old.array in this case.
		return slice{unsafe.Pointer(&zerobase), old.len, cap}
	}

	newcap := old.cap
	doublecap := newcap + newcap
	if cap > doublecap {
		newcap = cap
	} else {
		if old.len < 1024 {
			newcap = doublecap
		} else {
			// Check 0 < newcap to detect overflow
			// and prevent an infinite loop.
			for 0 < newcap && newcap < cap {
				newcap += newcap / 4
			}
			// Set newcap to the requested cap when
			// the newcap calculation overflowed.
			if newcap <= 0 {
				newcap = cap
			}
		}
	}

	var overflow bool
	var lenmem, newlenmem, capmem uintptr
	// Specialize for common values of et.size.
	// For 1 we don't need any division/multiplication.
	// For sys.PtrSize, compiler will optimize division/multiplication into a shift by a constant.
	// For powers of 2, use a variable shift.
	switch {
	case et.size == 1:
		lenmem = uintptr(old.len)
		newlenmem = uintptr(cap)
		capmem = roundupsize(uintptr(newcap))
		overflow = uintptr(newcap) > maxAlloc
		newcap = int(capmem)
	case et.size == sys.PtrSize:
		lenmem = uintptr(old.len) * sys.PtrSize
		newlenmem = uintptr(cap) * sys.PtrSize
		capmem = roundupsize(uintptr(newcap) * sys.PtrSize)
		overflow = uintptr(newcap) > maxAlloc/sys.PtrSize
		newcap = int(capmem / sys.PtrSize)
	case isPowerOfTwo(et.size):
		var shift uintptr
		if sys.PtrSize == 8 {
			// Mask shift for better code generation.
			shift = uintptr(sys.Ctz64(uint64(et.size))) & 63
		} else {
			shift = uintptr(sys.Ctz32(uint32(et.size))) & 31
		}
		lenmem = uintptr(old.len) << shift
		newlenmem = uintptr(cap) << shift
		capmem = roundupsize(uintptr(newcap) << shift)
		overflow = uintptr(newcap) > (maxAlloc >> shift)
		newcap = int(capmem >> shift)
	default:
		lenmem = uintptr(old.len) * et.size
		newlenmem = uintptr(cap) * et.size
		capmem, overflow = math.MulUintptr(et.size, uintptr(newcap))
		capmem = roundupsize(capmem)
		newcap = int(capmem / et.size)
	}

	// The check of overflow in addition to capmem > maxAlloc is needed
	// to prevent an overflow which can be used to trigger a segfault
	// on 32bit architectures with this example program:
	//
	// type T [1<<27 + 1]int64
	//
	// var d T
	// var s []T
	//
	// func main() {
	//   s = append(s, d, d, d, d)
	//   print(len(s), "\n")
	// }
	if overflow || capmem > maxAlloc {
		panic(errorString("growslice: cap out of range"))
	}

	var p unsafe.Pointer
	if et.ptrdata == 0 {
		p = mallocgc(capmem, nil, false)
		// The append() that calls growslice is going to overwrite from old.len to cap (which will be the new length).
		// Only clear the part that will not be overwritten.
		memclrNoHeapPointers(add(p, newlenmem), capmem-newlenmem)
	} else {
		// Note: can't use rawmem (which avoids zeroing of memory), because then GC can scan uninitialized memory.
		p = mallocgc(capmem, et, true)
		if lenmem > 0 && writeBarrier.enabled {
			// Only shade the pointers in old.array since we know the destination slice p
			// only contains nil pointers because it has been cleared during alloc.
			bulkBarrierPreWriteSrcOnly(uintptr(p), uintptr(old.array), lenmem-et.size+et.ptrdata)
		}
	}
	memmove(p, old.array, lenmem)

	return slice{p, old.len, newcap}
}

```
{% endnoteblock %}

扩容规则：新的slice的容量大于等于原来的slice的1.25倍或2倍。当slice容量小于1024的时候，新的容量是原来的2倍，但是超过了1024之后，先是扩容到原来的1.25倍容量，之后还要进行一次内存对齐。

## 注意点

1. 将一个切片作为参数与将一个切片的指针作为参数的区别：
  1. 将切片作为参数，如果我们切片进行扩容或者底层数组的指向改变，那么调用者对应的切片指向的底层数组不会改变
  2. 将切片指针作为参数，如果切片


## 参考
1. [深度解密Go语言之Slice](https://mp.weixin.qq.com/s/MTZ0C9zYsNrb8wyIm2D8BA)
2. [码洞《深度解析 Go 语言中「切片」的三种特殊状态》](https://juejin.im/post/5bea58df6fb9a049f153bca8)