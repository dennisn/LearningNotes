# Getting started with Kafka

## Publisher-Subscriber system
  - Useful for system comprised of micro-services: avoid complex dependencies & configurations --> Decouple the dependencies
    + Scalable: multiple micro-services of the same type may joinly process the same "message queue"
  - Some similar Pub-Sub system: RabbitMQ, Mulesoft, Redis, Amazon SQS, Amazon SNS
  
## Kafka basic
  - Apache project
  - Often use with Apache Zookeeper (installed in odd-number) for distributed configuration
  - Kafka message: sent to a specified topics, contains:
    + Headers: Key: string -> Value: anything
	+ Body: Key: anything -> Value: anything
	+ Timestamp: Epoch
  - Zookeeper: to keep track of the shared configuration parameters: what's the IP of each Kafka service, partitions, etc.

### Topics, Partitions and Rebalance
  - Topic can be pre-created, or auto-created when first message is sent
  - Topics can be partitioned based on message key
  - Consumers read from partitions (by assignment), and Kafka keeps track of messages offset (i.e. position of last read) per consumers
    + More partitions enable more consumers reading in parallel
  - Partitions & topics are replicated across brokers for high availability