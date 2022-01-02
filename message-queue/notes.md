
## Message Queues 
- A message queue is a form of asynchronous service-to-service communication used in serverless and microservices architectures. Messages are stored on the queue until they are processed and deleted. Each message is processed only once, by a single consumer.
- Foundation: Synchronous vs asynchronous
- ![message-queue](message-queues.png)

- Many producers and consumers can use the queue, but each message is processed only once, by a single consumer. For this reason, this messaging pattern is often called one-to-one, or point-to-point, communications. When a message needs to be processed by more than one consumer, message queues can be combined with Pub/Sub messaging in a fanout design pattern.

- Must watch
  - https://youtu.be/FMhbR_kQeHw
  - https://aws.amazon.com/message-queue/
  - https://aws.amazon.com/blogs/aws/aws-lambda-adds-amazon-simple-queue-service-to-supported-event-sources/
    - sqs
      - dead letter queue to handle un-processed data
      - server side encryption
      - long polling to support destination failure
      - easy monitoring and cloud watch notification

## Pub/Sub
- diff b/w pub/sub and observable design pattern -  https://medium.com/easyread/difference-between-pub-sub-pattern-and-observable-pattern-d5ae3d81e6ce
- comparing-publish-subscribe-messaging-and-message - https://dzone.com/articles/comparing-publish-subscribe-messaging-and-message
- Example: `aws sns`
- fully managed and durable with automated scaling : message will never be lost
- topics and subscription based
- youtube.com/watch?v=LkmWLl35VjU&list=PL9nWRykSBSFg-CziAHKjr0XnvghEVkpFi&index=2
- youtube.com/watch?v=mXk0MNjlO7A
- Fault talarant mechanism in distributed system


## Design Message Queue

### Functional requirement
- send message
- receive message
- Pull vs Push 
- Message ordering (FIFO)

### Non functional requirement
- Scalable (Must be able to handle large no of request )
- Avaialble (Must be able to handle hardware/network failure to meet the request)
- Durability (Message submitted should not get lost before the consumer can consume it)
- High performant
- Avoid Message duplication and processing of same message by multiple consumer
- Message delivery sematics (Handleing undelivered message) (at most once, at least once, exactly once),
- Monitoring and security

![high-level](.\Message-queue-high-level.png)

#### Meta data service management
1. Distributed cache cluster consists of multiple host with same data replicated among all the nodes
2. The cache is splitted into multiple shards and stored in each node inside the cluster. Now, each shard can have multiple replicas with ledaer and follower pattern. There could be a configuration service like zookeeper to handle each shard cluster. Client (front end service) needs to decide which shards to connect to. 
3.  The cache is splitted into multiple shards and stored in each node inside the cluster. Once receiving any request host node will forward the request to appropriate shards. 


#### Back end service management
1. **Back end service single cluster with multiple nodes**
   1. Following leader follower pattern for each queue.
   2. replication between leader and followers could be in asynchronous or sync way.Sync replication ensures durability of the message at a cost of latency.
   3. There is a need of cluster configuration manager to monitor the health of cluster and keeping track of which queue is handled by which nodes. e.g. zookeeper
2. **Back end service with multiple clusters with multiple nodes**
   1. Each cluster handle different sets of queues.
   2. Each queue could be further partitioned and shared acorss multiple clusters. 
   3. Each nodes in a cluster will have the exactly same data replicated.


#### Front end service
![front_end](.\message-queue-front-service.PNG)

watch - https://www.youtube.com/watch?v=iJLL-KPqBpM
