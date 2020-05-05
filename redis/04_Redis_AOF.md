## AOF append only file

redis的写操作记录到文件中,不会修改文件

### 1. 优缺点

优点：

* 丢失数据少

* redis中，RDB和AOF可以同时开启

```text
如果开启了AOF，只会用AOF恢复

4.0以后，AOF中包含RDB全量，增量记录新的写操作：先把RDB二进制恢复，然后增量恢复AOF
```


缺点：

* 体量无无限变大 -> 恢复慢

```text
日志，优点如果能保住，还是可以用的
结果：设计一个方案让日志，AOF足够小
```

### 2. 一个场景

```text
redis运行了10年
开启了AOF
10年头，redis挂了
1，AOF多大：很大，10T
*，恢复，会不会溢出
2，恢复要多久：恢复用5年
```



### 3. 解决弊端

* HDFS的做法:

```text
hdfs，fsimage+edits.log
让日志只记录增量合并的过程
```

* Redis的做法：

4.0以前：

重写：
```text
删除抵消的命令，合并重复的命令，最终也是一个纯指令的日志文件

但是恢复的成本仍然很高
```
4.0以后：

重写：
```text
先将老的数据RDB到aof文件中，再将增量的以指令的方式Append到AOF

结论：AOF是一个混合体，既利用了RDB的恢复快，也利用了日志的全量
```


### 4. 调整写的策略

redis是内存数据库，写操作会触发IO，调整策略

/etc/redis/6379.conf,找到 APPEND ONLY MODE
```text
appendonly yes    #默认是关闭的
appendfilename "appendonly.aof"

auto-aof-rewrite-percentage 100  #记忆最后一次重写的体积大小
auto-aof-rewrite-min-size 64mb   #到这么大了就重写

#appendfsync always
appendfsync everysec   #默认每秒
#appendfsync no        
```

* appendfsync always|everysec|no的说明

redis写磁盘的过程：

`redis -> kernel fd8 buffer -> flush -> 磁盘`

```text
appendfsync always # redis每写一个就flush到磁盘，最可靠,最多丢一条
appendfsync everysec # 每秒flush一下，可能丢失差一点buffer就满的数据
appendfsync no #不调用flush，buffer满了就往磁盘刷，可能会丢失最后一个buffer
```


### 5. AOF和RDB混合使用

/etc/redis/6379.conf

```text
aof-use-rdb-preamble yes
```

一个操作示例：

```text
set k1 xxx
set k1 bbb

bgrewriteaof

此时AOF文件内容就是RDB二进制内容

set k1 w
set k1 m

此时AOF文件追加了set的明文
```

