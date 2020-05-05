## Redis数据类型

### 0. key

key是一个对象，属性举例如下

```text
type:string
encoding
```

### 1. String

#### 1.1 字符串

* 方法

```text
set
get
append
setrange
getrange
strlen
```

#### 1.2 数值

* 方法

```text
incr
```

* 场景：

```text
抢购，秒杀，详情页，点赞，评论
规避并发下，
对数据库的事务操作
完全由redis内存操作代替
```

#### 1.3 bitmap

* 方法

```text
setbit
bitcount
bitpos
bitop
```

* 场景1：

```text
有用户系统，统计用户登录天数，且窗口随机
setbit sean 1 1
setbit sean 7 1
setbit sean 364 1
STRLEN sean
BITCOUNT sean -2 -1
```

* 场景2：

```text
京东618做活动：送礼物
大库备货多少礼物
假设京东有2E用户

僵尸用户
冷热用户/忠诚用户

活跃用户统计！随即窗口
比如说 1号~3号  连续登录要     去重

setbit 20190101   1  1
setbit 20190102   1  1
setbit 20190102   7  1
bitop  or   destkey 20190101  20190102
BITCOUNT  destkey  0 -1 

```

### 2. List

* 特性：

```text
栈，同向命令
队列，反向命令
数组，使用LINDEX命令
阻塞队列，单播队列，blpop命令
```


### 3. Hash


* 方法

* 场景：

```text
一个对象多个属性，对field进行数值计算：点赞，收藏，详情页
```


### 4. Set

* 方法

```text
spop #取出1个
```

* 特性：

```text
【无序】&&【随机性】放入的多少不同，元素存储的顺序不同;去重
```

* 操作：

```text
集合交并差操作
```

* 场景-随机事件：

```text
SRANDMEMBER  key  count
正数：取出一个去重的结果集（不能超过已有集）
负数：取出一个带重复的结果集，一定满足你要的数量
如果：0，不返回
```

### 5. Sorted Set(zset)

* 特性

```text
物理内存左小右大
不随命令发生变化
zrang
zrevrang
```


* 操作：

集合操作:并集，交集

权重/聚合指令:

```text
zadd k1 80 tom 60 sean 70 baby
zadd k2 60 tom 100 sean 40 yiming
zunionstore unkey 2 k1 k2 [weights 1 0.5 ] [aggregate max]
zrange unkey 0 -1 withscore
```

* 排序是怎么实现的?增删改查的速度？

使用跳表(skip list),插入后随机造层
