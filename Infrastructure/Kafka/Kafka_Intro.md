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
  
## Kafka connect
  - Connectors provided by 3rd party to import data into/export data from Kafka
    + Connectors registry "https://www.confluent.io/product/connectors"
  - Connector is run by "task", which is assigned to "worker" (i.e. similar to docker container)
    + allow task rebalance: failed worker will have its tasks re-distributed to live ones
  - Can use schema to validate/help with serialize/deserialize data from Kafka
    + How to: 
      - set `KEY/VALUE_SERIALIZER_CLASS_CONFIG` to `io.confluent.kafka.serializers.KafkaAvroSerializer.class` on producer
      - set `KEY/VALUE_DESERIALIZER_CLASS_CONFIG` to `io.confluent.kafka.serializers.KafkaAvroSerializer.class` on consumer
      - Then set "schame.registry.url"
      - For consumer, also set `specific.avro.reader` to "true"

## Kafka stream
  - Kafka stream app: something that may read from/write to a Kafka topic(s) (i.e. same or different topics)
    + Setup properties: 
      - `APPLICATION_ID_CONFIG`: name of application
      - `DEFAULT_KEY_SERDE_CLASS_CONFIG`: Key serialize/deserialize
      - `DEFAULT_Value_SERDE_CLASS_CONFIG`: Value serialize/deserialize
  - Using Kafka stream to coordinate multi-phase transactions (e.g. multi-micro services)
    + All events are recorded in Kafka topic --> proof of transactions
    + Resumption at point of disconnect --> recovery
    + De-coupling between services --> scalability (i.e. one service can be served by multiple instances)
  - Some key points:
    + Kafka can be used as WAL (Write Ahead Log) for Kafka stream apps
    + A Kafka stream app: a "topology" of functions to a stream of message
    + We can deploy as many streams as we want --> they act as microservices
    + KSQL is the CLI to query KSQLDB, which is aa thin layer over Kafka stream --> allows us to do simple stuff without creating a stream apps in code

## Misc
  - For high-availability, nodes are often in odd number
    + when communication is disrupted, at least one group will have majority of nodes, and can continues with changes, while the minority group will switch to read-only mode until re-connection
  - Apache Avro: used to define schema in registry for Kafka. It has 3 basic modes of operations:
    + Generic -> to validate schema
    + Specific: use AVSC file and codegen capabilities -> generate code
    + Reflection: based on a class to create AVSC file