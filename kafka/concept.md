# Kafka

## 名词解释

```text
1.producer：
　　消息生产者，发布消息到 kafka 集群的终端或服务。
2.broker：
　　kafka 集群中包含的服务器。
3.topic：
　　每条发布到 kafka 集群的消息属于的类别，即 kafka 是面向 topic 的。
4.partition：
　　partition 是物理上的概念，每个 topic 包含一个或多个 partition。kafka 分配的单位是 partition。
5.consumer：
　　从 kafka 集群中消费消息的终端或服务。
6.Consumer group：
　　high-level consumer API 中，每个 consumer 都属于一个 consumer group，每条消息只能被 consumer group 中的一个 Consumer 消费，但可以被多个 consumer group 消费。
7.replica：
　　partition 的副本，保障 partition 的高可用。
8.leader：
　　replica 中的一个角色， producer 和 consumer 只跟 leader 交互。
9.follower：
　　replica 中的一个角色，从 leader 中复制数据。
10.controller：
　　kafka 集群中的其中一个服务器，用来进行 leader election 以及 各种 failover。
12.zookeeper：
　　kafka 通过 zookeeper 来存储集群的 meta 信息。
```

## 为什么要使用 Kafka，为什么要使用消息队列

* 缓冲和削峰

>上游数据时有突发流量，下游可能扛不住，或者下游没有足够多的机器来保证冗余，Kafka在中间可以起到一个缓冲的作用，把消息暂存在Kafka中，下游服务就可以按照自己的节奏进行慢慢处理。

* 解耦和扩展性

>项目开始的时候，并不能确定具体需求。消息队列可以作为一个接口层，解耦重要的业务流程。只需要遵守约定，针对数据编程即可获取扩展能力。

* 冗余

> 可以采用一对多的方式，一个生产者发布消息，可以被多个订阅topic的服务消费到，供多个毫无关联的业务使用。

* 健壮性

>消息队列可以堆积请求，所以消费端业务即使短时间死掉，也不会影响主要业务的正常进行。

* 异步通信

>很多时候，用户不想也不需要立即处理消息。消息队列提供了异步处理机制，允许用户把一个消息放入队列，但并不立即处理它。想向队列中放入多少消息就放多少，然后在需要的时候再去处理它们。

## Kafka 为什么那么快

* Cache Filesystem Cache PageCache缓存

* 顺序写 由于现代的操作系统提供了预读和写技术，磁盘的顺序写大多数情况下比随机写内存还要快。

* Zero-copy 零拷技术减少拷贝次数

* Batching of Messages 批量量处理。合并小的请求，然后以流的方式进行交互，直顶网络上限。

* Pull 拉模式 使用拉模式进行消息的获取消费，与消费端处理能力相符。

## Kafka的message格式是什么样的

一个Kafka的Message由一个固定长度的header和一个变长的消息体body组成

* header部分由一个字节的magic(文件格式)和四个字节的CRC32(用于判断body消息体是否正常)构成。

>当magic的值为1的时候，会在magic和crc32之间多一个字节的数据：attributes(保存一些相关属性，比如是否压缩、压缩格式等等);如果magic的值为0，那么不存在attributes属性

* body是由N个字节构成的一个消息体，包含了具体的key/value消息

## Kafka中consumer group 是什么概念

逻辑上的概念，是Kafka实现单播和广播两种消息模型的手段。

同一个topic的数据，会广播给不同的group；同一个group中的worker，只有一个worker能拿到这个数据。

换句话说，对于同一个topic，每个group都可以拿到同样的所有数据，但是数据进入group后只能被其中的一个worker消费。

group内的worker可以使用多线程或多进程来实现，也可以将进程分散在多台机器上，worker的数量通常不超过partition的数量，且二者最好保持整数倍关系，因为Kafka在设计时假定了一个partition只能被一个worker消费（同一group内）

> 即：所有consumer group中的consumer使用一套offset。

## Kafka中的消息是否会丢失和重复消费

要确定Kafka的消息是否丢失或重复，从两个方面分析入手：消息发送和消息消费。

* 消息发送

Kafka消息发送有两种方式：同步（sync）和异步（async），默认是同步方式，可通过producer.type属性进行配置。Kafka通过配置request.required.acks属性来确认消息的生产：

        0---表示不进行消息接收是否成功的确认；
        1---表示当Leader接收成功时确认；
       -1---表示Leader和Follower都接收成功时确认；
       
综上所述，有6种消息生产的情况，下面分情况来分析消息丢失的场景：

    （1）acks=0，不和Kafka集群进行消息接收确认，则当网络异常、缓冲区满了等情况时，消息可能丢失；

    （2）acks=1、同步模式下，只有Leader确认接收成功后但挂掉了，副本没有同步，数据可能丢失；

* 消息消费

Kafka消息消费有两个consumer接口，Low-level API和High-level API：

    Low-level API：消费者自己维护offset等值，可以实现对Kafka的完全控制；

    High-level API：封装了对parition和offset的管理，使用简单；

如果使用高级接口High-level API，可能存在一个问题就是当消息消费者从集群中把消息取出来、并提交了新的消息offset值后，还没来得及消费就挂掉了，那么下次再消费时之前没消费成功的消息就“诡异”的消失了；


解决办法：

    针对消息丢失：同步模式下，确认机制设置为-1，即让消息写入Leader和Follower之后再确认消息发送成功；异步模式下，为防止缓冲区满，可以在配置文件设置不限制阻塞超时时间，当缓冲区满时让生产者一直处于阻塞状态；

    针对消息重复：将消息的唯一标识保存到外部介质中，每次消费时判断是否处理过即可。


## 防止重复消费
防止重复消费有2中方式

1. 去掉消费者的自动提交,每处理完一条消息就手动commit一次
2. 在消费者这边做幂等操作

## 消费者幂等操作
* 比如拿个数据要写库，你先根据主键查一下，如果这数据都有了，你就别插入了，update一下好
* 比如是写redis，那没问题了，反正每次都是set，天然幂等性
* 需要让生产者发送每条数据的时候，里面加一个全局唯一的id，类似订单id之类的东西，然后你这里消费到了之后，先根据这个id去比如redis里查一下，之前消费过吗？如果没有消费过，你就处理，然后这个id写redis。如果消费过了，那你就别处理了，保证别重复处理相同的消息即可。
* 基于数据库的唯一键来保证重复数据不会重复插入多条，重复数据拿到了以后我们插入的时候，因为有唯一键约束了，所以重复数据只会插入报错，不会导致数据库中出现脏数据


## Kafka producer 打数据，ack  为 0， 1， -1 的时候代表啥， 设置 -1 的时候，什么情况下，leader 会认为一条消息 commit了

* 1（默认）  数据发送到Kafka后，经过leader成功接收消息的的确认，就算是发送成功了。在这种情况下，如果leader宕机了，则会丢失数据。

* 0 生产者将数据发送出去就不管了，不去等待任何返回。这种情况下数据传输效率最高，但是数据可靠性确是最低的。

* -1 producer需要等待ISR中的所有follower都确认接收到数据后才算一次发送完成，可靠性最高。当ISR中所有Replica都向Leader发送ACK时，leader才commit，这时候producer才能认为一个请求中的消息都commit了。


## Kafka中的ISR、AR又代表什么？ISR的伸缩又指什么

* ISR:In-Sync Replicas 副本同步队列
* AR:Assigned Replicas 所有副本

>ISR是由leader维护，follower从leader同步数据有一些延迟（包括延迟时间replica.lag.time.max.ms和延迟条数replica.lag.max.messages两个维度, 当前最新的版本0.10.x中只支持replica.lag.time.max.ms这个维度），任意一个超过阈值都会把follower剔除出ISR, 存入OSR（Outof-Sync Replicas）列表，新加入的follower也会先存放在OSR中。AR=ISR+OSR。

## 如果leader crash时，ISR为空怎么办

Kafka在Broker端提供了一个配置参数：unclean.leader.election,这个参数有两个值：
* true（默认）

>允许不同步副本成为leader，由于不同步副本的消息较为滞后，此时成为leader，可能会出现消息不一致的情况。

* false

>不允许不同步副本成为leader，此时如果发生ISR列表为空，会一直等待旧leader恢复，降低了可用性。


## Kafka中的broker 是干什么的

broker 是消息的代理，Producers往Brokers里面的指定Topic中写消息，Consumers从Brokers里面拉取指定Topic的消息，然后进行业务处理，broker在中间起到一个代理保存消息的中转站。

## Kafka中的 zookeeper 起到什么作用，可以不用zookeeper么

zookeeper 是一个分布式的协调组件，早期版本的Kafka用zk做meta信息存储，consumer的消费状态，group的管理以及 offset的值。考虑到zk本身的一些因素以及整个架构较大概率存在单点问题，新版本中逐渐弱化了zookeeper的作用。新的consumer使用了Kafka内部的group coordination协议，也减少了对zookeeper的依赖，

但是broker依然依赖于ZK，zookeeper 在Kafka中还用来选举controller 和 检测broker是否存活等等


## 为什么Kafka不支持读写分离

在 Kafka 中，生产者写入消息、消费者读取消息的操作都是与 leader 副本进行交互的，从 而实现的是一种主写主读的生产消费模型。

Kafka 并不支持主写从读，因为主写从读有 2 个很明 显的缺点:

* 数据一致性问题

>数据从主节点转到从节点必然会有一个延时的时间窗口，这个时间 窗口会导致主从节点之间的数据不一致。某一时刻，在主节点和从节点中 A 数据的值都为 X， 之后将主节点中 A 的值修改为 Y，那么在这个变更通知到从节点之前，应用读取从节点中的 A 数据的值并不为最新的 Y，由此便产生了数据不一致的问题。

* 延时问题

>类似 Redis 这种组件，数据从写入主节点到同步至从节点中的过程需要经 历网络→主节点内存→网络→从节点内存这几个阶段，整个过程会耗费一定的时间。而在 Kafka 中，主从同步会比 Redis 更加耗时，它需要经历网络→主节点内存→主节点磁盘→网络→从节 点内存→从节点磁盘这几个阶段。对延时敏感的应用而言，主写从读的功能并不太适用。

## Kafka中是怎么体现消息顺序性的

Kafka每个partition中的消息在写入时都是有序的，消费时，每个partition只能被每一个group中的一个消费者消费，保证了消费时也是有序的。

整个topic不保证有序。如果为了保证topic整个有序，那么将partition调整为1

## 消费者提交消费位移时提交的是当前消费到的最新消息的offset还是offset+1

offset+1


##直接内存和零拷贝

