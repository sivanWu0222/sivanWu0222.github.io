---
title: linux下的五种网络IO模型以及select/poll/epoll区别
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
  - Linux
  - 网络
tags:
  - IO
  - 多路复用
  - 面试
date: 2021-03-19 13:25:51
summary:
---

## 前提知识：了解同步/异步，阻塞/非阻塞
参考：[聊聊同步、异步、阻塞与非阻塞](https://www.jianshu.com/p/aed6067eeac9)


## 正题：5种IO模型
1. 同步阻塞
2. 同步非阻塞
3. 异步IO
4. IO多路复用
5. 信号驱动IO

参考：[聊聊Linux 五种IO模型](https://www.jianshu.com/p/486b0965c296)


## select/poll/epoll区别

select, poll，以及epoll都是多路复用。
> [配合视频享用更加](https://www.bilibili.com/video/BV1qJ411w7du?from=search&seid=16339864339742641939)


这里插入我们的pdf图。。

**场景：设计一个高性能的网络服务器，可以向多个客户端提供同时连接，并且可以处理这些客户端的请求。如何设计呢？**

<!-- more -->

**两种方案：**
1. 多线程：每一个请求让一个线程去处理，但是需要线程频繁上下文切换，代价比较高
2. 单线程（如果A传上来的消息正在处理，这个时候B传上来消息是否会丢失？不会丢失，因为迎接B传上来的数据这个人不是CPU,是我们专注于IO的DMA控制器来保证。这个过程中，cpu只是在开始和结束的时候做一下简单的中断处理，）

**DMA补充：**
1. “Direct Memory Access（存储器直接访问）。这是指一种高速的数据传输操作，允许在外部设备和存储器之间直接读写数据。整个数据传输操作在一个称为"DMA控制器"的控制下进行的。CPU除了在数据传输开始和结束时做一点处理外（开始和结束时候要做中断处理），在传输过程中CPU可以进行其他的工作（前提是未设置停止CPU访问）。这样，在大部分时间里，CPU和输入输出都处于并行操作。因此，使整个计算机系统的效率大大提高”。 
2. DMA传送方式是让存储器与外设、或外设与外设之间直接交换数据，不需要经过CPU的累加器中转，减少了这个中间环节，并且内存地址的修改、传送完毕的结束报告都是由硬件电路实现的，因此大大地提高了数据的传输速度。一个DMA传送只需要执行一个DMA周期，相当于一个总线读写周期。
3. DMA是在专门的硬件（ DMA）控制下，实现高速外设和主存储器之间自动成批交换数据尽量减少CPU干预的输入/输出操作方式。 


## 总结：

- select，poll，epoll 都是 IO 多路复用的机制
- IO 多路复用就是实现**一个进程可以监视多个文件句柄**（socket、文件或者管道等等），一旦某个文件句柄就绪，就能够通知程序进行相应的**读写操作**。
- select，poll，epoll 都是同步 IO。所谓同步 IO，便是读写是阻塞的

### select、poll和epoll的区别

| IO多路复用模型         | select                                                       | poll                                       | epoll                                                        |
| ---------------------- | ------------------------------------------------------------ | ------------------------------------------ | ------------------------------------------------------------ |
| **最大连接数限制**     | 内核对 fd_set 集合的大小，为1024                             | 无最大连接限制，原因是它是基于链表来存储的 | 连接数有上限，1/2G内存的机器上可以打开10/20万左右的连接。    |
| **连接大带来的IO效率** | 每次调用时都会对连接进行线性遍历，所以随着FD的增加会造成遍历速度慢的“线性下降性能问题” | 同select                                   | epoll内核中实现是根据每个fd上的callback函数来实现的，只有活跃的socket才会主动调用callback，所以在活跃socket较少没有线性下降的性能问题，但是所有socket都很活跃的情况下，可能会有性能问题。 |
| **消息传递方式**       | 内核需要将消息传递到用户空间，都需要内核拷贝动作             | 同select                                   | epoll通过内核和用户空间共享一块内存来实现的。                |
| **时间复杂度**         | O(n)                                                         | O(n)                                       | O(1)                                                         |
| **特点**               |                                                              |                                            |                                                              |
| **触发模式**           | 水平触发                                                     | 水平触发                                   | 水平触发LT和边缘触发ET                                       |
| **优点**               |                                                              |                                            |                                                              |
| **缺点**               | 1.调用 select 函数时，需要把 fd_set 集合从用户态拷贝到内核态，当 fd_set 集合很大时，这个开销将会非常巨大 <br>2.调用 select 函数时，需要在内核遍历传递进来的所有 fd_set，当 fd_set 集合很大时，这个开销将会非常巨大 内核对被监控的 fd_set 集合大小做了限制 |                                            |                                                              |

 **select，poll，epoll 之间的对比:**

1. IO 效率：select 只知道有 IO 事件发生，却不知道是哪几个流，只能采取轮询所有流的方式，故其具有 O(n) 的无差别轮询复杂度，处理的流越多，无差别轮询时间就越长；poll 与 select 并无区别，它的时间复杂度也是 O(n)；epoll 会将哪个流发生了怎样的 IO 事件通知我们（当描述符就绪时，系统注册的回调函数会被调用，将就绪描述符放到 readyList 里面），它是事件驱动的，其时间复杂度为 O(1)
2. 操作方式：select 和 poll 都是采取遍历的方式，而 epoll 则是采取了回调的方式
3. 底层实现：select 的底层实现为数组，poll 的底层实现为链表，而 epoll 的底层实现为[**红黑树**](https://www.jianshu.com/p/e136ec79235c)
4. 最大连接数：select 的最大连接数为 1024 或 2048，而 poll 和 epoll 是无上限的
5. 对描述符的拷贝：select 和 poll 每次被调用时都会把描述符集合从用户态拷贝到内核态，而 epoll 在调用 epoll_ctl 时会拷贝进内核并保存，之后每次 epoll_wait 时不会拷贝
6. 性能：**epoll 在绝大多数情况下性能远超 select 和 poll，但**在连接数少并且连接都十分活跃的情况下，select 和 poll 的性能可能比 epoll 好，因为 epoll 的通知机制需要很多函数回调

### 水平触发与边缘触发

#### 水平触发(level-trggered)

- 只要文件描述符关联的**读内核缓冲区非空**，有数据可以读取，就一直发出可读信号进行通知，
- 当文件描述符关联的**内核写缓冲区不满**，有空间可以写入，就一直发出可写信号进行通知

#### 边缘触发(edge-triggered)

- 当文件描述符关联的读内核缓冲区由空转化为非空的时候，则发出可读信号进行通知，
- 当文件描述符关联的内核写缓冲区由满转化为不满的时候，则发出可写信号进行通知

两者的区别在哪里呢**？水平触发是只要读缓冲区有数据，就会一直触发可读信号**，而**边缘触发仅仅在空变为非空的时候通知一次**

### select

#### 函数定义

```c
int select(int maxfdp1,fd_set *readset,fd_set *writeset,fd_set *exceptset,const struct timeval *timeout);
```

### poll

#### 函数定义

```c
int poll(struct pollfd *fds, nfds_t nfds, int timeout);
```

### epoll

#### 函数定义

```c
int epoll_create(int size);
//创建epoll实例，会创建所需要的红黑树，以及就绪链表，以及代表epoll实例的文件句柄
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event);
//对于添加到epfd的文件描述符fd, 添加或者删除或者修改， 对应的event
int epoll_wait(int epfd, struct epoll_event * events, int maxevents, int timeout);
//等待注册的事件发生，返回事件的数目，并将触发的事件写入events数组中
```

### 参考

- [面试必备：对 select，poll，epoll 的详细解析](https://www.codenong.com/cs105364662/)
- [Linux面试必知: 一句话讲透epoll](https://www.jianshu.com/p/41dc33b97419)
