## JVM参数实例

```text
-verbose:gc # 在控制台输出GC情况
-Xloggc:/home/work/logs/applogs/gc.log.%p # 将GC日志输出到指定文件中
-XX:+PrintGCDetails # 打印gc日志
-XX:+PrintGCTimeStamps # 打印每个独立的GC线程任务的时间戳
-XX:+PrintHeapAtGC  # 每一次GC前和GC后，都打印堆信息
-XX:+PrintGCApplicationStoppedTime # 打印上次GC暂停到目前的时间
-XX:+PrintGCDateStamps # 打印每个GC的日期时间戳
-XX:+PrintAdaptiveSizePolicy # 打印自适应调整策略
-XX:+DisableExplicitGC # 禁止在启动期间显式调用System.gc()
-XX:+HeapDumpOnOutOfMemoryError # Error时将heap内存dump到当前目录的一个文件
-XX:HeapDumpPath=/home/work/logs/applogs/ #设置在dump heap时将文件dump到哪里
-XX:+UseG1GC # 设置使用G1作为GC收集器
```
