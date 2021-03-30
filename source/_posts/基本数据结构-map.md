---
title: 基本数据结构-map
author: yirufeng
pin: false
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
date: 2021-03-08 07:48:05
summary:
---

1. 什么是map
2. 为什么要用map
3. map的底层如何实现
  - map内存模型
  - 创建map
  - 哈希函数
  - key定位过程
  - map的两种get操作
  - 【面试重点】如何进行扩容
  - 【重点】map的遍历
  - map的赋值
  - map的删除
4. map进阶
  - 可以边遍历边删除么
  - key可以是float类型么
5. 总结
6. 参考资料

> 面试经常被问到golang map的扩容，特此记录下来


## map的结构
> golang中map是采用哈希查找表以及链表法来解决冲突，

{% note info, 查看map内部的构造：golang 1.15.5 的 runtime/map.go 中查看源码  %}

```go
// A header for a Go map.
type hmap struct {
	// Note: the format of the hmap is also encoded in cmd/compile/internal/gc/reflect.go.
	// Make sure this stays in sync with the compiler's definition.
	count     int // # live cells == size of map.  Must be first (used by len() builtin)
	flags     uint8
	B         uint8  // log_2 of # of buckets (can hold up to loadFactor * 2^B items)
	noverflow uint16 // approximate number of overflow buckets; see incrnoverflow for details
	hash0     uint32 // hash seed

	buckets    unsafe.Pointer // array of 2^B Buckets. may be nil if count==0.
	oldbuckets unsafe.Pointer // previous bucket array of half the size, non-nil only when growing
	nevacuate  uintptr        // progress counter for evacuation (buckets less than this have been evacuated)

	extra *mapextra // optional fields
}
```
{% noteblock, 结构剖析，hmap 共有9个属性： %}
1. count，表示map中键值对的个数
2. flags，标志位，表示当前map的状态
3. B，表示我们map中元素的个数，buckets的对数log_2
4. noverflow，overflow的bucket的近似数
5. hash0，计算key的时候传入我们hash函数的一个随机种子
6. buckets，指向我们旧桶，也就是还没有搬迁的
7. oldbuckets，扩容的时候oldbuckets将会是buckets的两倍
8. nevacuate，指示扩容进度，小于此地址的buckets搬迁完成
{% endnoteblock %}

## map中key定位
过程：
1. 用户传入的key经过哈希计算(需要两个参数，一个是key，一个是hmap的hash0字段的值)之后获得哈希值，共64个Bit，
2. 最后的B（hmap结构体中B的值）个bit位决定落在哪个桶。
3. 同时哈希值的高8位为此key在bucket中的位置（tophash），如果对应的bucket没找到还要去找后面的overflow bucket。

## 【重点】map扩容
> 通过汇编语言可以找到赋值操作对应源码中的函数是 `mapassign`

### 时机
在向map中插入新key的时候，会进行条件检测，符合下面两个条件，就会触发扩容：
1. 装载因子（计算公式：`localFactor = count/(2^B)`）超过阈值，源码里定义的阈值是 6.5。
2. overflow 的 bucket 数量过多：当 B 小于 15，也就是 bucket 总数 2^B 小于 2^15 时，如果 overflow 的 bucket 数量超过 2^B；当 B >= 15，也就是 bucket 总数 2^B 大于等于 2^15，如果 overflow 的 bucket 数量超过 2^15。

**如下是判断是否需要扩容的代码：**
```go
  // Did not find mapping for key. Allocate new cell & add entry.
	// If we hit the max load factor or we have too many overflow buckets,
	// and we're not already in the middle of growing, start growing.
	if !h.growing() && (overLoadFactor(h.count+1, h.B) || tooManyOverflowBuckets(h.noverflow, h.B)) {
		hashGrow(t, h)
		goto again // Growing the table invalidates everything, so try again
	}
```

**计算装载因子的函数：**
```go
// overLoadFactor reports whether count items placed in 1<<B buckets is over loadFactor.
func overLoadFactor(count int, B uint8) bool {
	return count > bucketCnt && uintptr(count) > loadFactorNum*(bucketShift(B)/loadFactorDen)
}
```

**判断是否有太多的overflow buckets：**
```go
// tooManyOverflowBuckets reports whether noverflow buckets is too many for a map with 1<<B buckets.
// Note that most of these overflow buckets must be in sparse use;
// if use was dense, then we'd have already triggered regular map growth.
func tooManyOverflowBuckets(noverflow uint16, B uint8) bool {
	// If the threshold is too low, we do extraneous work.
	// If the threshold is too high, maps that grow and shrink can hold on to lots of unused memory.
	// "too many" means (approximately) as many overflow buckets as regular buckets.
	// See incrnoverflow for more details.
	if B > 15 {
		B = 15
	}
	// The compiler doesn't see here that B < 16; mask B to generate shorter shift code.
	return noverflow >= uint16(1)<<(B&15)
}
```

流程：![pfVzhx](https://cdn.jsdelivr.net/gh/sivanWu0222/ImageHosting@master/uPic/pfVzhx.png)


解释一下：

1. 第1个条件：我们知道，每个 bucket 有 8 个空位，在没有溢出，且所有的桶都装满了的情况下，装载因子算出来的结果是 8。因此当装载因子超过 6.5 时，表明很多 bucket 都快要装满了，查找效率和插入效率都变低了。在这个时候进行扩容是有必要的。
2. 第2个条件：是对第 1 点的补充。就是说在装载因子比较小的情况下，这时候 map 的查找和插入效率也很低，而第 1 点识别不出来这种情况。表面现象就是计算装载因子的分子比较小，即 map 里元素总数少，但是 bucket 数量多（真实分配的 bucket 数量多，包括大量的 overflow bucket）。

不难想像造成这种情况的原因：不停地插入、删除元素。先插入很多元素，导致创建了很多 bucket，但是装载因子达不到第 1 点的临界值，未触发扩容来缓解这种情况。之后，删除元素降低元素总数量，再插入很多元素，导致创建很多的 overflow bucket，但就是不会触犯第 1 点的规定，你能拿我怎么办？overflow bucket 数量太多，导致 key 会很分散，查找插入效率低得吓人，因此出台第 2 点规定。这就像是一座空城，房子很多，但是住户很少，都分散了，找起人来很困难。

对于命中条件 1，2 的限制，都会发生扩容。但是扩容的策略并不相同，毕竟两种条件应对的场景不同。

对于条件 1，元素太多，而 bucket 数量太少，很简单：将 B 加 1，bucket 最大数量（2^B）直接变成原来 bucket 数量的 2 倍。于是，就有新老 bucket 了。注意，这时候元素都在老 bucket 里，还没迁移到新的 bucket 来。而且，新 bucket 只是最大数量变为原来最大数量（2^B）的 2 倍（2^B * 2）。

对于条件 2，其实元素没那么多，但是 overflow bucket 数特别多，说明很多 bucket 都没装满。解决办法就是开辟一个新 bucket 空间，将老 bucket 中的元素移动到新 bucket，使得同一个 bucket 中的 key 排列地更紧密。这样，原来，在 overflow bucket 中的 key 可以移动到 bucket 中来。结果是节省空间，提高 bucket 利用率，map 的查找和插入效率自然就会提升。

搬迁的目的就是将老的 buckets 搬迁到新的 buckets。而通过前面的说明我们知道，应对条件 1，新的 buckets 数量是之前的一倍，应对条件 2，新的 buckets 数量和之前相等。

对于条件 1，从老的 buckets 搬迁到新的 buckets，由于 bucktes 数量不变，因此可以按序号来搬，比如原来在 0 号 bucktes，到新的地方后，仍然放在 0 号 buckets。

对于条件 2，就没这么简单了。要重新计算 key 的哈希，才能决定它到底落在哪个 bucket。例如，原来 B = 5，计算出 key 的哈希后，只用看它的低 5 位，就能决定它落在哪个 bucket。扩容后，B 变成了 6，因此需要多看一位，它的低 6 位决定 key 落在哪个 bucket。这称为 rehash。


再来看一下扩容具体是怎么做的。**由于 map 扩容需要将原有的 key/value 重新搬迁到新的内存地址，如果有大量的 key/value 需要搬迁，会非常影响性能。因此 Go map 的扩容采取了一种称为“渐进式”地方式，原有的 key 并不会一次性搬迁完毕，每次最多只会搬迁 2 个 bucket。**

            
// 指向 x，y 中的 key
            xk
,
 yk 
unsafe
.
Pointer
            
// 指向 x，y 中的 value
            xv
,
 yv 
unsafe
.
Pointer
        
)
        
// 默认是等 size 扩容，前后 bucket 序号不变

## 【重点】map的遍历
> 注意，遍历都是遍历的 bucket 指针，也就是所谓的新 buckets，oldbuckets指向的是旧桶
{% note info, 当我们在遍历 map 时，并不是固定地从 0 号 bucket 开始遍历，每次都是从一个随机值序号的 bucket 开始遍历，并且是从这个 bucket 的一个随机序号的 cell 开始遍历。这样，即使你是一个写死的 map，仅仅只是遍历它，也不太可能会返回一个固定序列的 key/value 对了。  %}


1. 初始化startBucket(也就是从哪个桶开始遍历)以及offset（从哪个cell开始，对应桶的第1个元素经过的偏移）
2. 从startBucket中的offset对应元素开始遍历，一直遍历到overflow buckets为空
3. 之后遍历下一个bmap中的桶，如果此时startBucket为最后一个则回到开头，也就是循环遍历，直到所有桶都遍历完成，
  1. 来到一个桶首先要检查是否搬迁
  2. 如果没有搬迁，则要取出搬迁后分配到新的startBucket桶的那些元素
  3. 不断循环

{% note info, note info 默认主题色，适合中性的信息 %}
{% noteblock, 标题（可选） %}

Windows 10不是為所有人設計,而是為每個人設計

{% endnoteblock %}

{% folding green, 查看代码测试 %}

{% endfolding %}







搬迁的目的就是将老的 buckets 搬迁到新的 buckets。而通过前面的说明我们知道，应对条件 1，新的 buckets 数量是之前的一倍，应对条件 2，新的 buckets 数量和之前相等。

对于条件 1，从老的 buckets 搬迁到新的 buckets，由于 bucktes 数量不变，因此可以按序号来搬，比如原来在 0 号 bucktes，到新的地方后，仍然放在 0 号 buckets。

对于条件 2，就没这么简单了。要重新计算 key 的哈希，才能决定它到底落在哪个 bucket。例如，原来 B = 5，计算出 key 的哈希后，只用看它的低 5 位，就能决定它落在哪个 bucket。扩容后，B 变成了 6，因此需要多看一位，它的低 6 位决定 key 落在哪个 bucket。这称为 rehash。


上面说的 hashGrow() 函数实际上并没有真正地“搬迁”，它只是分配好了新的 buckets，并将老的 buckets 挂到了 oldbuckets 字段上。真正搬迁 buckets 的动作在 growWork() 函数中，而调用 growWork() 函数的动作是在 mapassign 和 mapdelete 函数中。也就是插入或修改、删除 key 的时候，都会尝试进行搬迁 buckets 的工作。先检查 oldbuckets 是否搬迁完毕，具体来说就是检查 oldbuckets 是否为 nil。

理解了上面 bucket 序号的变化，我们就可以回答另一个问题了：为什么遍历 map 是无序的？

map 在扩容后，会发生 key 的搬迁，原来落在同一个 bucket 中的 key，搬迁后，有些 key 就要远走高飞了（bucket 序号加上了 2^B）。而遍历的过程，就是按顺序遍历 bucket，同时按顺序遍历 bucket 中的 key。搬迁后，key 的位置发生了重大的变化，有些 key 飞上高枝，有些 key 则原地不动。这样，遍历 map 的结果就不可能按原来的顺序了。

源码部分比较好看懂，尤其是理解了前面注释的几段代码后，再看这部分代码就没什么压力了。所以，接下来，我将通过图形化的方式讲解整个遍历过程，希望能够清晰易懂。


## 迭代：
