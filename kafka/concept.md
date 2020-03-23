#kafka

##为什么要使用 kafka，为什么要使用消息队列

* 缓冲和削峰

>上游数据时有突发流量，下游可能扛不住，或者下游没有足够多的机器来保证冗余，kafka在中间可以起到一个缓冲的作用，把消息暂存在kafka中，下游服务就可以按照自己的节奏进行慢慢处理。

* 解耦和扩展性

>项目开始的时候，并不能确定具体需求。消息队列可以作为一个接口层，解耦重要的业务流程。只需要遵守约定，针对数据编程即可获取扩展能力。

* 冗余

> 可以采用一对多的方式，一个生产者发布消息，可以被多个订阅topic的服务消费到，供多个毫无关联的业务使用。

* 健壮性

>消息队列可以堆积请求，所以消费端业务即使短时间死掉，也不会影响主要业务的正常进行。

* 异步通信

>很多时候，用户不想也不需要立即处理消息。消息队列提供了异步处理机制，允许用户把一个消息放入队列，但并不立即处理它。想向队列中放入多少消息就放多少，然后在需要的时候再去处理它们。

## kafka 为什么那么快

* Cache Filesystem Cache PageCache缓存

* 顺序写 由于现代的操作系统提供了预读和写技术，磁盘的顺序写大多数情况下比随机写内存还要快。

* Zero-copy 零拷技术减少拷贝次数

* Batching of Messages 批量量处理。合并小的请求，然后以流的方式进行交互，直顶网络上限。

* Pull 拉模式 使用拉模式进行消息的获取消费，与消费端处理能力相符。


##防止重复消费
防止重复消费有2中方式

1. 去掉消费者的自动提交,每处理完一条消息就手动commit一次
2. 在消费者这边做幂等操作

##消费者幂等操作
* 比如拿个数据要写库，你先根据主键查一下，如果这数据都有了，你就别插入了，update一下好
* 比如是写redis，那没问题了，反正每次都是set，天然幂等性
* 需要让生产者发送每条数据的时候，里面加一个全局唯一的id，类似订单id之类的东西，然后你这里消费到了之后，先根据这个id去比如redis里查一下，之前消费过吗？如果没有消费过，你就处理，然后这个id写redis。如果消费过了，那你就别处理了，保证别重复处理相同的消息即可。
* 基于数据库的唯一键来保证重复数据不会重复插入多条，重复数据拿到了以后我们插入的时候，因为有唯一键约束了，所以重复数据只会插入报错，不会导致数据库中出现脏数据


##Kafka中的ISR、AR又代表什么？ISR的伸缩又指什么

* ISR:In-Sync Replicas 副本同步队列
* AR:Assigned Replicas 所有副本

>ISR是由leader维护，follower从leader同步数据有一些延迟（包括延迟时间replica.lag.time.max.ms和延迟条数replica.lag.max.messages两个维度, 当前最新的版本0.10.x中只支持replica.lag.time.max.ms这个维度），任意一个超过阈值都会把follower剔除出ISR, 存入OSR（Outof-Sync Replicas）列表，新加入的follower也会先存放在OSR中。AR=ISR+OSR。


##kafka中的broker 是干什么的

broker 是消息的代理，Producers往Brokers里面的指定Topic中写消息，Consumers从Brokers里面拉取指定Topic的消息，然后进行业务处理，broker在中间起到一个代理保存消息的中转站。

##kafka中的 zookeeper 起到什么作用，可以不用zookeeper么

zookeeper 是一个分布式的协调组件，早期版本的kafka用zk做meta信息存储，consumer的消费状态，group的管理以及 offset的值。考虑到zk本身的一些因素以及整个架构较大概率存在单点问题，新版本中逐渐弱化了zookeeper的作用。新的consumer使用了kafka内部的group coordination协议，也减少了对zookeeper的依赖，

但是broker依然依赖于ZK，zookeeper 在kafka中还用来选举controller 和 检测broker是否存活等等

