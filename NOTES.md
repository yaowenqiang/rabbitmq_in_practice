## Queues

queue is a collection of entities maintained in a sequence, Sequence can be modified by

+ adding entities to the begining or to the end
+ removing from the begining or from the end

### Queue attributes

+ Queue size - Number of messages in the queue
+ Queue age(time) - Age of the oldest message in the queue

### Use case for Queues

+ Decompose two systes
+ Estimate desired system performance
+ Increase fault tolenrance leel (not the same as ihgh availability)
+ Increases loose coupling and scalability(horizontal scaling, autoscaling)
+ Increase system's inbound traffic
+ Buffer system's outbound bytes


### RabbitMQ

The concurrency in Erlang is performed by the process. But Erlang processes are slightly different then most people used to.Erlang process are rather an activity or task run in parallel and executed indepently from the other processes

### Erlang processes

+ are very light(memory footprint)
+ start very fast
+ operate in isolation from other processes
+ are scheduled by Erlang's Virtual Machine (default for Erlang process for RabbitMQ is 1 million)

### Similar products

+ IBM MQ
+ Amazon MQ Kinesis
+ Google Cloud Pub/Sub
+ Kafka
+ Apache ActiveMQ
+ Tibco EMS/Messaging/Rendezvous
+ alibaba Mmessage Queue


### RabbitMA, Kafka, AWS Kinesis

|           | RabbitMQ | Kafka | Aws Kinesis |
| --------------- | --------------- | --------------- | --------------- |
| Released1 | June, 2007 | Janary 2011 | Novermber 2013 |
| General Purpose | Messages broker(queue) | Messages bus (stream processing - log) | Messages bus (stream process - log) |
| Messages replay | no | yes | yes |
| Cunsumer | Dummy consumer / Push model | Smart consumer(Pull mode) | Smart consumer(Pull & Push models) |
| Data Throughput (relative to simple shard/node) | High - ( No quoted throughput) | Hightest (No qouted throughput) | High+(one shard can support 1 MB/s input, 2 MB/s output or 1000 records per seconds) |
| Data consistency | Highest (ACKs) | High+ | High+ |
| Persistence perio | No mimit | No limit | Up to 7 days |
| Maintence effort | High+ | Highest | Very Low |
| Cost | Relatively small | Relatively small | Highest |
| OPen Source | Yes | Yes | No |

 ### RabbitMQ vs Kafka - deep dive

|        | RabbitMQ | Kafka |
| --------------- | --------------- | --------------- |
| Distribution | many consumers per queue, but message is consumed only once | Consumers distributed by topic partitions, message can be consumed by many consumers |
| Availability | highly abailable | high available, but Zookeeper is needed to manage cluster state |
| Replication | queues are not replicated by design | by desgin |
| Protocols | standard Queue protocols like AMQP, STOMP, HTTP, and MQTT | binary serialized data |
| Acknowdgments | sophisticated | basic |
| Routing | very flexible (exchange, binding keys) | message is send to the topic by a key |

## RabbitMQ Deep Dive

### AMQP Protocol

AMQP is an open standard application layer protocol

+ Desigined for asyncchronous communication
+ AMQP standardizes the behavior of the messaging publisher and consumer
+ Platform independent
+ Technology independent (many SDKS)

### AMQP Message

+ meader - Metadata, key/value paires, Defined by AMQP specification
+ properties - Properties: Metadata, Key/Value paires, Application-specific information holder
+ Body - Payload bytes[](max 2G )

Message limit is 2GB, but it's better to avoid such a big messages if possible. Message is send epr frames, 131KB by default






