#提交到远程spark集群
```
spark-submit --master spark://10.234.10.62:7077 --class com.test.scala.SparkJSON2SQLTest --total-executor-cores 1 spark-java-test-1.0-SNAPSHOT.jar
```
#提交到本地master
```
spark-submit --master local[2]  --class com.test.java.SparkSQLTest4jOnline spark-java-test-1.0-SNAPSHOT.jar --total-executor-cores 2
```
