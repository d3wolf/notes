#1.启动kafka
`<KAFKA_HOME>\bin\windows`
```
kafka-server-start.bat .\config\server.properties
```

#2.创建topic
`<KAFKA_HOME>\bin\windows>`
```
kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic monitorupload
```
```
kafka-topics.bat --create --zookeeper 10.234.10.58:2181 --replication-factor 1 --partitions 1 --topic fangliang-test
```

##查看topic
```
kafka-topics.bat --zookeeper 10.234.10.58:2181 --list
kafka-topics.bat --list --bootstrap-server 10.234.10.58:9092
```

#3.打开一个Producer
`<KAFKA_HOME>\bin\windows>`
```
kafka-console-producer.bat --broker-list localhost:9092 --topic monitorupload
kafka-console-producer.bat --broker-list 10.234.10.58:9092 --topic fangliang-test
```

#4.打开一个Consumer
`<KAFKA_HOME>\bin\windows>`
```
kafka-console-consumer.bat --bootstrap-server localhost:9092 --from-beginning --topic monitorupload
kafka-console-consumer.bat --bootstrap-server 10.234.10.58:9092 --topic fangliang-test --from-beginning
```

## 新消费者列表查询（支持0.9版本+）
```
kafka-consumer-groups.bat --new-consumer --bootstrap-server 10.234.10.58:9092 --list
```
## 新消费者列表查询（支持0.10版本+）
```
kafka-consumer-groups.bat --bootstrap-server 10.234.10.58:9092 --list
```
