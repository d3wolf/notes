## IO模型演化

![](../images/IO演化1.png)

### 1.BIO

```text
JVM: 一个线程的成本  1MB
1，线程多了调度成本CPU浪费
2，内存成本
```

![](../images/IO演化-BIO.png)


### 2.同步非阻塞NIO

```text
同步非阻塞   NIO
如果有1000fd
代表用户进程轮询调用1000次kernel
成本问题
```

![](../images/IO演化-同步非阻塞NIO.png)


线程换1000个fd到内核查，内核返回可read的fd

```text
fd相关数据在用户空间和内核空间拷贝
粒度不够细
```
![](../images/IO演化-同步非阻塞NIO-2.png)


### 3.多路复用epoll

epoll的三个调用
```c
int epoll_create(int size);  // 创建一个epoll的句柄,
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event); //epoll的事件注册函数，它不同于select()是在监听事件时告诉内核要监听什么类型的事件，而是在这里先注册要监听的事件类型
int epoll_wait(int epfd, struct epoll_event * events, int maxevents, int timeout); // 收集在epoll监控的事件中已经发送的事件
```

内核空间做共享空间的增删改操作，内核和用户空间都可以查共享空间

![](../images/IO演化-多路复用NIO-2.png)



## select poll epoll区别


(1)select==>时间复杂度O(n)

它仅仅知道了，有I/O事件发生了，却并不知道是哪那几个流（可能有一个，多个，甚至全部），我们只能无差别轮询所有流，找出能读出数据，或者写入数据的流，对他们进行操作。所以select具有O(n)的无差别轮询复杂度，同时处理的流越多，无差别轮询时间就越长。

(2)poll==>时间复杂度O(n)

poll本质上和select没有区别，它将用户传入的数组拷贝到内核空间，然后查询每个fd对应的设备状态， 但是它没有最大连接数的限制，原因是它是基于链表来存储的.

(3)epoll==>时间复杂度O(1)

epoll可以理解为event poll，不同于忙轮询和无差别轮询，epoll会把哪个流发生了怎样的I/O事件通知我们。所以我们说epoll实际上是事件驱动（每个事件关联上fd）的，此时我们对这些流的操作都是有意义的。（复杂度降低到了O(1)）

select，poll，epoll都是IO多路复用的机制。I/O多路复用就通过一种机制，可以监视多个描述符，一旦某个描述符就绪（一般是读就绪或者写就绪），能够通知程序进行相应的读写操作。但select，poll，epoll本质上都是同步I/O，因为他们都需要在读写事件就绪后自己负责进行读写，也就是说这个读写过程是阻塞的，而异步I/O则无需自己负责进行读写，异步I/O的实现会负责把数据从内核拷贝到用户空间。  
