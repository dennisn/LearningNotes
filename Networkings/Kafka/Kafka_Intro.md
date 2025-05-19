# Getting started with Kafka

## Publisher-Subscriber system
  - Useful for system comprised of micro-services: avoid complex dependencies & configurations --> Decouple the dependencies
    + Scalable: multiple micro-services of the same type may joinly process the same "message queue"
  - Some similar Pub-Sub system: RabbitMQ, Mulesoft, Redis, Amazon SQS, Amazon SNS
  
## Kafka basic
  - Apache project
  - Often use with Apache Zookeeper (installed in odd-number) for distributed configuration
  - Kafka message:
    + Headers: Key: string -> Value: anything
	+ Body: Key: anything -> Value: anything
	+ Timestamp: Epoch

### Topics, Partitions and Rebalance
  - Topics can be partitioned by message key
  - Consumers read from partitions, and Kafka keeps track of messages offset (i.e. position of last read) per consumers
    + More partitions enable more consumers
  - Partitions & topics are replicated across brokers for high availability