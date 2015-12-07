---
layout: post
title: How to use Kafka’s High Level Consumer in Scala
categories: kafka
abstract: "learning kafka"
comments: true
tags: []
image:
  feature:
  credit: 
  creditlink: 
date: 2015-12-07T23:11:41+08:00
author: mark yang
---


#How to use Kafka’s High Level Consumer in Scala
##Why using the high level consumer
 * Sometimes, we hope read messages through multiple threads, we care about the data is consumed rather than care about the order. 
 * High Level Consumer stores last offset read from a specific partition in ZooKeeper, it would be re-consumed the msg from the last offset.
 
here are some very specific rules from official documents:

* if you provide more threads than there are partitions on the topic, some threads will never see a message
* if you have more partitions than you have threads, some threads will receive data from multiple partitions
* if you have multiple partitions per thread there is NO guarantee about the order you receive messages, other than that within the partition the offsets will be sequential. For example, you may receive 5 messages from partition 10 and 6 from partition 11, then 5 more from partition 10 followed by 5 more from partition 10 even if partition 11 has data available.
* adding more processes/threads will cause Kafka to re-balance, possibly changing the assignment of a Partition to a Thread.


##Step 1:  Setting your configurations
```scala
val props = new java.util.Properties();
props.put("zookeeper.connect", a_zookeeper);
props.put("group.id", a_groupId);
props.put("zookeeper.session.timeout.ms", "400");
props.put("zookeeper.sync.time.ms", "200");
props.put("auto.commit.interval.ms", "1000");
val config= new kafka.consumer.ConsumerConfig(props);
```


 - zookeeper.connect: Zookeeper host string
 - group.id: 	A string that uniquely identifies the group of consumer processes to which this consumer belongs. By setting the same group id multiple processes indicate that they are all part of the same consumer group.
 - zookeeper.session.timeout.ms: ZooKeeper session timeout. If the consumer fails to heartbeat to ZooKeeper for this period of time it is considered dead and a rebalance will occur.
 - zookeeper.sync.time.ms: How far a ZK follower can be behind a ZK leader
 - auto.commit.interval.ms: The frequency in milliseconds that the consumer offsets are auto-committed to Kafka if *enable.auto.commit* is set to true.

##Step 2: Creating consumer client 
```scala
val consumer=kafka.consumer.Consumer.create(config)
```


##Step 3: Creating the thread pool
```scala
val topic="yourTopic"
val numThread=1
val topicCounts=Map(topic->numThread)
val consumerMap=consumer.createMessageStreams(topicCounts)
```
## Step4: Print the messages
```scala
val consumerIterator=consumerMap.get(topic).get.head.iterator()  

val msgs=consumerIterator.map(_.message())

//the msg is a Array[Byte] type , 
//if you want to convert the Array[Byte] to a String, 
//you can using the new String(bytes)
msgs.foreach(msg=>println(new String(msg)))
```



