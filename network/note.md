## 负载均衡

* 地域上的负载均衡 

DNS

* 硬件层面的负载均衡 - F5 A10

集群间的负载均衡

* 软件层面的负载均衡 - LVS Nginx

主机之间的负载均衡 


## 缓存部分

* Redis 集群搭建


## 数据库

主从

高可用

分库分表 



## etcd集群搭建

* CPU 要求

小型集群 2-4 core

大型集群 8-16 core

* 内存要求

8G

16-64G

* 磁盘要求

IOPS  SSD最好

IOPS（Input/Output Operations Per Second）是一个用于计算机存储设备（如硬盘（HDD）、固态硬盘（SSD）或存储区域网络（SAN））性能测试的量测方式，可以视为是每秒的读写次数


修改hostname

hostnamectl set-hostname Etcd-Node01-Master

修改ip地址

vim /etc/sysconfig/network-scripts/ifcfg-ens33


yum install -y etcd


修改etcd配置
/etc/etcd/etcd.conf

```
#[Member]
#ETCD_CORS=""
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
#ETCD_WAL_DIR=""
ETCD_LISTEN_PEER_URLS="http://10.0.0.30:2380"
ETCD_LISTEN_CLIENT_URLS="http://10.0.0.30:2379,http://127.0.0.1:2379"
#ETCD_MAX_SNAPSHOTS="5"
#ETCD_MAX_WALS="5"
ETCD_NAME="Master"
#ETCD_SNAPSHOT_COUNT="100000"
#ETCD_HEARTBEAT_INTERVAL="100"
#ETCD_ELECTION_TIMEOUT="1000"
#ETCD_QUOTA_BACKEND_BYTES="0"
#ETCD_MAX_REQUEST_BYTES="1572864"
#ETCD_GRPC_KEEPALIVE_MIN_TIME="5s"
#ETCD_GRPC_KEEPALIVE_INTERVAL="2h0m0s"
#ETCD_GRPC_KEEPALIVE_TIMEOUT="20s"
#
#[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://10.0.0.30:2380"
ETCD_ADVERTISE_CLIENT_URLS="http://10.0.0.30:2379"
#ETCD_DISCOVERY=""
#ETCD_DISCOVERY_FALLBACK="proxy"
#ETCD_DISCOVERY_PROXY=""
#ETCD_DISCOVERY_SRV=""
ETCD_INITIAL_CLUSTER="Master=http://10.0.0.30:2380,Node02=http://10.0.0.31:2380,Node03=http://10.0.0.32:2380"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_INITIAL_CLUSTER_STATE="new"
#ETCD_STRICT_RECONFIG_CHECK="true"
#ETCD_ENABLE_V2="true"
#
#[Proxy]
#ETCD_PROXY="off"
#ETCD_PROXY_FAILURE_WAIT="5000"
#ETCD_PROXY_REFRESH_INTERVAL="30000"
#ETCD_PROXY_DIAL_TIMEOUT="1000"
#ETCD_PROXY_WRITE_TIMEOUT="5000"
#ETCD_PROXY_READ_TIMEOUT="0"
#
#[Security]
#ETCD_CERT_FILE=""
#ETCD_KEY_FILE=""
#ETCD_CLIENT_CERT_AUTH="false"
#ETCD_TRUSTED_CA_FILE=""
#ETCD_AUTO_TLS="false"
#ETCD_PEER_CERT_FILE=""
```

```text
systemctl restart etcd
etcdctl member list
```
