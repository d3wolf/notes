## 命令

* 服务命令
```text
zkServer.sh start|stop|restart #启动|停止|重启

zkServer.sh status #查看zk节点信息，服务状态

```

* 客户端命令

```text
zkCli.sh –server 127.0.0.1:2181 #连接到 ZooKeeper 服务

ls /              #显示根目录下、文件,使用 ls 命令来查看当前 ZooKeeper 中所包含的内容
create /zk "test" #创建文件，并设置初始内容：创建一个新的 znode节点“ zk ”以及与它关联的字符串
get /zk           #获取文件内容： 确认 znode 是否包含我们所创建的字符串
set /zk "zkbak"   #对 zk 所关联的字符串进行设置
delete /zk        #将刚才创建的 znode 删除
stat /zk          #查看节点状态
setAcl            #设置节点acl
sync /zookeeper   #强制同步
quit              #退出客户端
help              #帮助命令
```

* 常用四字命令

> ZooKeeper 支持某些特定的四字命令字母与其的交互。它们大多是查询命令，用来获取 ZooKeeper 服务的当前状态及相关信息。用户在客户端可以通过 telnet 或 nc 向 ZooKeeper 提交相应的命令

```text
1. 可以通过命令：echo stat|nc 127.0.0.1 2181 来查看哪个节点被选择作为follower或者leader
2. 使用echo ruok|nc 127.0.0.1 2181 测试是否启动了该Server，若回复imok表示已经启动。
3. echo dump| nc 127.0.0.1 2181 ,列出未经处理的会话和临时节点。
4. echo kill | nc 127.0.0.1 2181 ,关掉server
5. echo conf | nc 127.0.0.1 2181 ,输出相关服务配置的详细信息。
6. echo cons | nc 127.0.0.1 2181 ,列出所有连接到服务器的客户端的完全的连接 / 会话的详细信息。
7. echo envi |nc 127.0.0.1 2181 ,输出关于服务环境的详细信息（区别于 conf 命令）。
8. echo reqs | nc 127.0.0.1 2181 ,列出未经处理的请求。
9. echo wchs | nc 127.0.0.1 2181 ,列出服务器 watch 的详细信息。
10. echo wchc | nc 127.0.0.1 2181 ,通过 session 列出服务器 watch 的详细信息，它的输出是一个与 watch 相关的会话的列表。
11. echo wchp | nc 127.0.0.1 2181 ,通过路径列出服务器 watch 的详细信息。它输出一个与 session 相关的路径。 
```

* znode节点的状态信息

> 使用get命令获取指定节点的数据时, 同时也将返回该节点的状态信息, 称为Stat. 其包含如下字段:
  
```text
czxid. 节点创建时的zxid.
mzxid. 节点最新一次更新发生时的zxid.
ctime. 节点创建时的时间戳.
mtime. 节点最新一次更新发生时的时间戳.
dataVersion. 节点数据的更新次数.
cversion. 其子节点的更新次数.
aclVersion. 节点ACL(授权信息)的更新次数.
ephemeralOwner. 如果该节点为ephemeral节点, ephemeralOwner值表示与该节点绑定的session id. 如果该节点不是ephemeral节点, ephemeralOwner值为0. 至于什么是ephemeral节点, 请看后面的讲述.
dataLength. 节点数据的字节数.
numChildren. 子节点个数.
```

## 角色

```text
leader 就是我们说的主；
follower 就是我们说的从；
observer 可以认为是主的clone copy，不参与投票
```
## 节点状态

```text
– looking 选举状态，当前群龙无首；
– leading leader才有的状态；
– following follower才有的状态；
```

## zxid

每次写成功的消息，都有一个全局唯一的标识，叫zxid。

> 是64bit的正整数，高32为叫epoch表示选举纪元，低32位是自增的id，每写一次加一。
可以想象为中国古代的年号，例如万历十五年，万历是epoch，十五年是id。

zk集群一般都是奇数个机器（2n+1）,只有一个主机leader，其余都是从机follower。选主还是写数据，要有>=n+1台选举相同，才能执行选举的操作。

投票优先级：优先比较zxid，如果相等，再比较机器的id，都按从大到小的顺序。

## PAXOS

https://www.douban.com/note/208430424/

当集群新建，或者主机死机，或者主机与一半或以上的从机失去联系后，都会触发选择新的主机操作。有两种算法fast paxos和basic paxos

## ZAB
zookeeper atomic broadcast

ZAB协议定义了选举（election）、发现（discovery）、同步（sync）、广播(Broadcast)四个阶段。
```text
选举（election）是选出哪台为主机；
发现（discovery）、同步（sync）当主选出后，要做的恢复数据的阶段；
广播(Broadcast)当主机和从选出并同步好数据后，正常的主写同步从写数据的阶段。
```
