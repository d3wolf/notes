#spark底层编程抽象之RDD是什么  
* A Resilient Distributed Dataset (RDD), the basic abstraction in Spark.Represents an immutable,partitioned collection of elements that can be operated on in parallel.
* RDD是以下三个单词的首字母缩写（Resilient Distributed Dataset），它表示弹性分布式数据集，它是spark最基本的数据抽象，它代表了一个不可变、可分区、里面的元素可以被并行操作的集合。
  * Dataset 数据集，在这里可以理解成它是一个集合，集合中存储了很多数据
  * Distributed 它的数据是进行了分布式存储，为了方便于后期进行分布式计算
  * Resilient 弹性，rdd的数据可以保存在内存中或者是磁盘中
 
