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
| Acknowledgments | sophisticated | basic |
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

## Install 

> 3.9.10

> rabbitmq-server

### Configuration

+ Environment variables - define ports, file locations and flags taken from the shell, or set in the environment Configuration file rabbitmq-env.conf/rabbitmq-env-conf.bat
+ Configuration file - defines server component settings for permissions, limits and clusters, and also plugin settings 
+ Runtime parameters and policies - defines cluster-wide settings which can change at runtime

Most important configuration variables

+ RABBITMQ_NODE_PORT (efault 5672) - used by AMQP o-g-1 and 1.0 clients(without TLS)
+ RABBITMQ_DIST_PORT (efault 20000 + RABBITMQ_NODE_PORT) - used by Erlang distribution for inter-node and CLI tools communications
+ RABBITMQ_NODENAME (Unix default: rabbit@$HOSTNAME, Windows default: rabbit@%COMPUTERNAME%) - uniqe node name
+ RABBITMQ_MNESIA_BASE - RabbitMQ server's node database, message store and cluster state files one for each node
+ RABBITMQ_MNESIA_DIR - This is RABBITMQ_MNESIA_BASE subfolder, includes a schema database, message stores, cluster member information and other persistent node state
+ RABBITMQ_LOG_BASE - just logs
+ RABBITMQ_CONFIG_FILE - points rabbitmq config file ( note: the 'confg' extension is added)
+ RABBITMQ_ENABLED_PLUGINS_FILE - file to track enabled plugins

### Most important config file entries

+ cluster_name(default "") - used for automatic clustering
+ listeners.tcp.default - same as RABBITMQ_NODE_PORT
+ heartbeat(default 60 seconds) - after 60s connection should be xonsidered unreachable by RabbitMQ and client libraries. Server suggest this value to client libraries while establishing TCP connections at AMQP(AMQP protocol level) Larger value imporves throughput, smaller value improves latency (not related with max_message size which is 2GB)
+ channel_max (default 0 - unlimited) - number of channels to negotiate with clients. Using more channels increases broker's memory footprint
+ management.tcp.port (dfault 15672) - the web-management and RESTFUL service

 
 > example config file

 > https://github.com/rabbitmq/rabbitmq-server/blob/main/deps/rabbit/docs/rabbitmq.conf.example


> docker run -it --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:4-management


## Installation - plugins

RabbitMQ supports plugins. Plugins extend core broker functionality in  many ways

+ more protocols
+ monitoring
+ additional AMQP 0-g exchange types
+ node fecerations
+ and many many more

>default memory limit is total memory * 40%


### Connection state

+ Running
+ Flow
+ Idle
+ Blocking/Blocked

### connections

TCP/IP connection between the client and the broker


### Channel

Virtual connection inside the physical TCP/IP connection (single TCP/IP connection with many channels vs many TCP/IP connections)

### RabbitMQ web admin

Web Admin is coming with RESTFUL service

> curl localhost:15672/api/nodes


> RabbitMQ Management HTTP API

>http://localhost:15672/api/index.html


## Core concepts

+ Producer - emits messages to exchange
+ Consumer - receives messages from the queue
+ Queue keeps/stores messages
+ Exchange
  + Binding - connects an exchange with a queue using binding key
  + Exchange compares routing key with binding keys (one-to-one or using pattern)

### Exchange 

+ Exchange type determines distribution model
+ Exchange types: nameless (empty string - default one), fanout, direct, topic, headers
+ Nameless exchange(aka 'default exchange' aka 'AMQP default')
  + Special one created by RabbitMQ
  + Compares routing key with queue name
  + Allows sending messages 'directly' to the queue (from the publisher perspective exchange is transpared)

#### fanout

+ Simply routes a received message to all queues that are bound to it, ignores routing key

#### Direct

+ Routes a received message to the queue that match 'routing-key' Nameless exchanges is a 'direct' exchange

#### Topic

+ Routes a received messages to queues, where binding key (idenfied as a pattern) matches to the routing key. Example, binding-key '*.og.error', routing-key ' application1.logs.erro'


#### Headers

+ Sample as topic, but binding-key is compared against 'any' or 'all' message headers (header x-match determines the behavior)

### Queue concept

+ Located on single node where it was declared and referenced by unique name. In contrary to Exchanges and Bindings, which exist on all nodes, Name can be provided or auto-generated by RabbitMQ Both. Producer and Consumer can create a queue

### Perforance

+ 1 quue = 1 Erlang processe
+ When node starts up to 16384 messages are load into RAMfrom the queue
+ Queue is ordered collection of messages
+ Messages are published and consumed in the FIFO manner (except proritized queues)
+ Queue has many properities
+ Depends from the use case we can set queue behavior
+ Queue can be federated or mirrored to increase reliability and availability
+ Internal queues amqp.
+ Queues prefixed by 'amq' are used for RabbitMQ internal purposes only

### Queue Properties

Among others RabbitMQ implements AMQP protocol, So, attributes (properties) and queue behavior can be changed in many ways to support generic RabbitMQ Architecture

By Common Queue definition

Name, Durable, Auto-delete, Exclusive

By protocol specific settings

Priority, etco

 By policies

TT, Federation etc (web management REST API command line tool)

> rabbitmqctl set_policy TTl ".*" '{'message-ttl': 6000}' -apply-to queues'


### Queue properties (most popular)

+ name - Name can be provided or auto-generate by RabbitMQ
+ Durable or not - Not durable queues won't survive broker restart
+ Auto Delete feature - Queues deletes itself where all consumers disconnecto
+ Classic or Quorom - Quorum  and Mirroring (policy)increases avaiability
+ Exclusive - Used by only one connection and the queue will be deleted when that connection closes
+ Priority - Additional CPUcost and increased latency. no guarantee of exact order (just string suggestion)
+ Exparation time (TTL) - Both messages (expiration property) and queues (x-message-ttl)can have TTL (policy settings), minimum value from both is ussed
+ Lazy Queues, Dead Letter Queues and many more

### Queue concept - order

Queues are FIFO manner - interms of producer (messages are always held in the queue in publication order), but not in terms of consumer

Section 4.7 of AMQP 0-9-1 core specification explains the conditions under which consuming order is guaranteed (received in the same order that they were send)

+ messages published in one channel
+ passing through one exchange
+ stored in one queue
+ consumed by exactly one outgoing channel

### ACK mode

+ Reject
+ Nack(negative ack - reject extension)
+ Ack
+ requeue
+ Dead Letter Exchange (DLX)

### Encoding 

how to handle binary payload


### Queue - Persistency and Durability

#### Durability

AMQP property for queues and exchanges. Messages in durable entities can survive serverrestarts, by being automaticly recreated when server gets up

#### Persistence

Messages property. Stored on disk in special persistency log file. allowing them to be restored once server gets up. Persistence has no effect a non-durable queues.

Persistent messages are removed from a durable queue once they are consumed (and Acknowlgdged)

As default messages won't survive RabbitMQ restart or entire server restart. To ensure messages survive, make sure to:

+ Send message as persistent message
+ Publish into durable exchange
+ Message to be stored is durable queue

### Work Queues 

+ Round-robin dispatching - Sends each message to the next consumer in a sequenceo
+ Message Acknowledgment - As default, message delivered into consumer is marked for deletiion
+ No message timeouts - RabbitMQ redeliver message when consumer dies
+ Prefetch - Rabbitmq dispatches a message when it enters the queue. here we want consumer to handle one message at time


### Publish / Subscribe (fanout)


+ Direct exchange routes to specific queue
+ Routing key is matched with binding key to route subset of messages to bound queues
+ Many categories of messages cause lot of bindings - it complicates administration of RabbitMQ

### Publish / Subscribe based on Topics

topic is a kinkd of routing key defined as list of words, delimited by dots
Binding is a simple regular expression where *(star) can substitute exactly one word, #(hash) can substitute zero or more words

+ event.sport.footbal
+ event.sport.footbal.fcbarcelona
+ event.sport
+ event.wather.local-alerts
+ event.weather.london


binding


+ *.sport.*
+ '.sport.#
+ *.weather.london.*

+ topic limit is 255 characters - List of words, delimited by dots
+ No words limit - A word can be any thing, should specify features hierarchy
+ Substitutions 
  + *(star) exactly one word
  + #(hash) zero or more words

### Publish / Subscribe based on Headers

#### binding Arguments

x-match = all
category = sport
source = bbc


### Summary

+ Uses headers from AMQP message structure - Key -> value paires
+ No substitutions - Header must exactly match to the list of headers defined in the binding
+ Ignores routing key - Like fanout exchange, headers exchange ignore routing key
+ More flexible than direct exchange - But sometimes harder to maintain

### RPC - Remote Procedure Call


+ RabbitMQ can be used as a nice RPCwrapper - Any RPC implementation based on queues is fine
+ Single queue - Rqeuests and responses goes through single queue, both matched by correlationid property

>Correlationid is one of 14 messages properties defined in AMQP 0-9-1 protocol (like deliveryMode, replyTo, contentType etc)


General RPC good habits

+ Make a clear comment in the code that function call is local or remote
+ Keep up-to-date documentation about dependencies between components
+ Handl communication issues, i.e when RPC server is down 
+ Scale-out RPC service by combining RPCpattern with Work Queues or more sophisticated exchanges like consistent hash 
+ and so on



TODO

### Custom exchanges - consistent hash

Sample usecases

+ Split long queue into smaller ones
+ Distribute logically related messages across many queues to reduce latency from teh consumer's perspective and increase reliability

> rabbitmq-plugins enable rabbitmq_consistent_hash_exchange


### Dead Letter Exchange(DLX)

Messages from the queue can be dead-lettered when

+ Message is negatively (ask)nowledged - When Nack or Reject with requeue-false is called
+ TTL expired - When message expires due to per-message TTL
+ Message is dropped - Queue exceeded length limit

+ Manage rejected messages - When Nack or Rejected with requeue-false is called
+ Manage queue capacity - When messages expires due to per-message TTL, or queue size exceeded length limit
+ Delay publication - intentional latency increase between publisher and consumer


### Delay retry / delay schedule with DLS

When  to use

+ No need or forbidden to consume messages immediately
  + Publish messages, but allow consumers to consume them after 3:00 PM Friday after conference pass
+ Automatic retrying
  + Rejected message to be resend again in about 10 minutes
+ Delay and 'batch' publication
  + intentional latency increase between publisher and consumer or group messages into bigger batches
  + Consider simple solutions over installing additional plugins, like rabbitmq-delayed-message-exchange Such plugins store messages in mnesia table, which is not designed to store high volume of the data


## Transactions and Publisher Confirms

### Data safety

Acknowledgments on both consumer and publisher side are important for data safety


+ Consumer Acknowledgment
  + One of ACK, nACK (RabbitMQ extension for Reject to reject multiple messages at once), Reject(defined in AMQP protocol)
  + defaults Auto Ack
+ Ack - Positive acknowledgment -> remove message from the queue
+ nACK or Reject - Negative acknowledgment with requeue=true -> keep message in the queue
+ nACK or Reject - Negative acknowledgment with requeue=false -> remove message from the queue
+ Transaction(tx)
  + Safe batching feature (remember batching Acks)
  + defaults Disabled
+ Publisher Confirms
  + Broker to send confirmation once message is safety stored (RabbitMQ feature)
  + Deafults disabled


  > Transactions decrease performance. Overall throughput is decreased by factor close to 250 Consider 'publisher confirms' or smart acknowledgments implementation

## RabbitMQ Vhosts

Virtual hosts are logical groups of entities
vhosts support multi-tenant Architecture.Vhosts can be an application name (app1, app2), environment(development, pipe, production)and so on
Default vhost is /(here why we use %2f in REST api requests)

Separate service, separate resources
Same service, separate resources








