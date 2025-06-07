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

