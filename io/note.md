##I/O笔记 

冯诺依曼

计算器，控制器
主存储设备
输入输出设备 I/O

抽象
一切皆文件

I/O

文件类型：
```text
- 普通文件
d 目录
b 块设备：区别于c，可以漂移读，来回切换读
I 连接：软链接，硬链接（两个文件名指向同一个文件，inode一样d）
c 字符设备：终端
s socket
p pipeline
[eventpoll]
```

执行` lsof -p $$` 后
可以看到类型

```
dd if=/dev/zero of=mydisk.img bs=1048576 count=100
```


fd
```text
/proc/$$/fd 当前进程所有fd

lsof -op $$
```

重定向
```text
错误输出重定向到标准输出，标准输出重定向到文件，顺序不能错
ls ./ /notexsitdir 1> ls.out 2>& 1
```

管道，父子进程
```text
export x 然后在子进程才能读到x

代码块
{ echo "dddd"; echo "sssss"; }

a=1
echo $a
{ a=9 ; echo "ssss"; } | cat 
echo $a
a=1  管道可以衔接输出和输入，左边和右边都是启动了子进程，所以a的值不变

echo $$ | cat 打印的是当前进程的pid，$$优先级高
echo $BASHPID | cat 打印的是管道左边子进程的pid
```

PageCache
```text
kernel折中方案
```

DMA(Direct Memory Access，直接存储器访问) 

> 是所有现代电脑的重要特色，它允许不同速度的硬件装置来沟通，而不需要依赖于 CPU 的大量中断负载。
>否则，CPU 需要从来源把每一片段的资料复制到暂存器，然后把它们再次写回到新的地方。
>在这个时间中，CPU 对于其他的工作来说就无法使用。



kernel
```
《深入理解linux系统》
《深入理解计算机系统》
```

虚拟内存地址->物理内存地址 映射过程，需要CPU里的MMU处理，

依赖的是page(4kb),不会全量分配，会有缺页，缺页中断


MMU（memory management unit，内存管理单元）

> 有时称作分页内存管理单元（英语：paged memory management unit，缩写为PMMU）。

> 它是一种负责处理中央处理器（CPU）的内存访问请求的计算机硬件。

> 它的功能包括虚拟地址到物理地址的转换（即虚拟内存管理）、内存保护、中央处理器高速缓存的控制，

> 在较为简单的计算机体系结构中，负责总线的仲裁以及存储体切换（bank switching，尤其是在8位的系统上）

BufferedWrite为什么比直接写要快
```text
因为在用户空间开辟了默认8kb的空间，每次写满这么多空间才会系统调用写到文件
```

epoll怎么知道数据到达的
```text
中断
```


NIO ByteBuffer三个指针
```text
position 
limit
capacity

写的时候compact，读的时候flip
```
