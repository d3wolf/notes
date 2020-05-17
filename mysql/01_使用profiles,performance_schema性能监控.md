
## 性能监控

### profile

使用show profile查询剖析工具，可以指定具体的type

```sql

set profiling=1;
select * from xxx;
show profiles;
show profile [ALL|CPU];
```

```text
all：显示所有性能信息  show profile all for query n
block io：显示块io操作的次数  show  profile block io for query n
context switches：显示上下文切换次数，被动和主动  show profile context switches for query n
cpu：显示用户cpu时间、系统cpu时间  show profile cpu for query n
IPC：显示发送和接受的消息数量  show profile ipc for query n
Memory：暂未实现
page faults：显示页错误数量  show profile page faults for query n
source：显示源码中的函数名称与位置  show profile source for query n
swaps：显示swap的次数 show profile swaps for query n
```



### performance schema

```sql
show databases;
use performance_schema;
show tables;
```

可以基于这个做BS监控页面

### 查看有多少个链接

使用show processlist查看连接的线程个数，来观察是否有大量线程处于不正常的状态或者其他不正常的特征
	
```text
show processlist;
```

属性说明

```text
id表示session id
user表示操作的用户
host表示操作的主机
db表示操作的数据库
command表示当前状态
    sleep：线程正在等待客户端发送新的请求
    query：线程正在执行查询或正在将结果发送给客户端
    locked：在mysql的服务层，该线程正在等待表锁
    analyzing and statistics：线程正在收集存储引擎的统计信息，并生成查询的执行计划
    Copying to tmp table：线程正在执行查询，并且将其结果集都复制到一个临时表中
    sorting result：线程正在对结果集进行排序
    sending data：线程可能在多个状态之间传送数据，或者在生成结果集或者向客户端返回数据
info表示详细的sql语句
time表示相应命令执行时间
state表示命令执行状态
```
