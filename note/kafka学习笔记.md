# 1.基本概念

#### Topic

Kafka将消息分门别类，每一类的消息称之为一个主题（Topic）。

#### Partition

parition 是物理上的概念，每个topic包含一个或多个partition，创建topic时可指定parition数量。每个partition对应于一个文件夹，该文件夹下存储该partition的数据和索引文件。

#### Replica

多副本（Replica）机制，通过增加副本数量可以提升容灾能力。同一分区的不同副本中保存的是相同的消息（在同一时刻，副本之间并非完全一样），副本之间是“一主多从”的关系，其中leader副本负责处理读写请求，follower副本只负责与leader副本的消息同步.

#### Producer

发布消息的对象称之为主题生产者（Kafka topic producer）

#### Consumer

订阅消息并处理发布的消息的对象称之为主题消费者（consumers）

#### Broker

已发布的消息保存在一组服务器中，称之为Kafka集群。集群中的每一个服务器都是一个代理（Broker）。 消费者可以订阅一个或多个主题（topic），并从Broker拉数据，从而消费这些已发布的消息。

#### AR（Assigned Replicas）

分区中的所有副本统称为AR（Assigned Replicas）

#### ISR（In-Sync Replicas）

所有与leader副本保持一定程度同步的副本（包括leader副本在内）组成ISR（In-Sync Replicas），ISR集合是AR集合中的一个子集

#### OSR（Out-of-Sync Replicas）

与leader副本同步滞后过多的副本（不包括leader副本）组成OSR（Out-of-Sync Replicas），由此可见，AR=ISR+OSR。

#### HW (High Watermark ) 俗称高水位

它标识了一个特定的消息偏移量（offset），消费者只能拉取到这个offset之前的消息。

#### LEO (Log End Offset)

它标识当前日志文件中下一条待写入消息的offset.



![image-20221120152126347](kafka%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.assets/image-20221120152126347.png)



如图 所示，它代表一个日志文件，这个日志文件中有 9 条消息，第一条消息的 offset（LogStartOffset）为0，最后一条消息的offset为8，offset为9的消息用虚线框表示，代表下一条待写入的消息。日志文件的HW为6，表示消费者只能拉取到offset在0至5之间的消息，而offset为6的消息对消费者而言是不可见的。LEO是Log End Offset的缩写，它标识当前日志文件中下一条待写入消息的offset，图中offset为9的位置即为当前日志文件的LEO，LEO的大小相当于当前日志分区中最后一条消息的offset值加1。分区ISR集合中的每个副本都会维护自身的LEO，而ISR集合中最小的LEO即为分区的HW，对消费者而言只能消费HW之前的消息。