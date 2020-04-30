## mysql索引

https://www.cnblogs.com/tanshaoshenghao/p/12572841.html

哈希虽然能够提供O(1)的单数据行操作性能，但是对于范围查询和排序却无法很好地支持，最终导致全表扫描；B树能够在非叶节点中存储数据，但是这也导致在查询连续数据时可能会带来更多的随机I/O，而B+树的所有叶节点可以通过指针相互连接，能够减少顺序遍历时产生的额外随机I/O；

B树

B+树

单表海量数据用MYISAM，没有外键

其他用innodb

mysql三层
client
server server端的连接器-》分析器-》优化器-》执行器
存储引擎


mysql执行计划
