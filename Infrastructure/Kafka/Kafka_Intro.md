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
    + One consumer could read from 1 or more partition, but 1 partition can be read by only 1 consumer of a consumer group (no sharing)
    + Hence more partitions enable more consumers reading in parallel
    + Rebalance is triggered when consumer leave/join the group, or their heartbeat was missing for too long
    + Rebalance: ensures all partitions are attended by a consumer in a group
  - Partitions & topics are replicated across brokers for high availability
  
## Misc
  - For high-availability, nodes are often in odd number
    + when communication is disrupted, at least one group will have majority of nodes, and can continues with changes, while the minority group will switch to read-only mode until re-connection