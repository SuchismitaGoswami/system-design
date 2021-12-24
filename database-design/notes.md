
## Database

### Type of database
- Relational
  - **schema** 
    - Structure: how data would be structured. The data used in the system has a fixed data model
    - Constraints: you can ensure some garbage data can be stored.
  - **acid property** -  Transactionality
  - **Pros**:
    - easy to perform vertical scaling
  - **Cons**
  - Difficult to perform horizontal scaling
  
- Non-relational
  - Key value store db (e.g. redis, memcache, dynamodb)
    - used: In caching solution
  - Document db
    - Pros:
      - Mostly used when there is no fixed schema
      - Fast read and write
      - Database sharding
      - Database scaling
    - Cons
      - Acid property is not provided by the dbms 

  - Columnar DB
    - Mostly used while there is a heavy write (storing health tracking data, analytical data)
    - Special Type of Heavy Reading: The table structure is defined based on the query.
    - It has a great support for distributed database
    - example. casandra 

  - Search DB 
    - Full text search queries are suppored by search databse
    - Indexes are created to make the searching faster
    - The data stored in the search DB is not the primary data store. Frequent datas or query data are stored in the search database

  - Graph databases (e.g. Neo4j)

  - Network db
  - File db


###  Database replication
If the replication lag is > t3 - t2
- Master/backup replication
- One master or leader node accepts the writes
- One or more backup/standby nodes that receive the updates from the master
- simple to implement, no conflict
- Multi master replication
- Multiple master or leader nodes accept the writes
- One or more backup/standby nodes that receive the updates from the master
- Need to resolve conflict

- Synchronous replication
- cansadra follows this replication
- Some databases such as postgress allows the master node to wait for some of the replication to get updated, not all

- Asynchronous replication
- host acknoledgement does not need to wait for standby or backup nodes to be updated. 
https://www.youtube.com/watch?v=aE2UPg3Ckck

- Pros and cons of database replication
  - Pros: 
    - Horizontal Scaling 
    - DB per region
  - Cons
    - Eventual consistency
    - Slow writes (synchronous replication)
    - Difficulties in replication in a Multi-master setup

- NOSQL - (BASE) Basically available Soft state Eventual consitency
- CAP Theorm (Consistency, Availability, Pertition to)
  - ![cap theorm](cap theorm.PNG)
  - We can tolerate the partioning by implementing eventual consistency.Thats when the link between the two datastore is established, then updates are propagated eventually. Based on the type of system, we have to decide whether we should decide which properties must be maintained.



### Data Consistency and Tradeoffs in Distributed Systems
- https://www.youtube.com/watch?v=m4q7VkgDWrM

- To avoid single point of failure on a database, we need to create multiple replicas across different region.
- Havimg multiple replicas leads to more complexity in keeping all replicas in sync, further data inconsistency if all of them accept write request and there is latency in transforming the updates from one replica to other.
- To solve this issue, one replica is made the leader/master and it will accept all the writes and others will only accept reads. However, transfer between the leader and the follower replicas takes time. So, this approach can not meet ACID property in case of relational db. To solve this problem, we introduce two phase locking.
  - **What is two phase locking?**
    - Helps achieve transactionality at the cost of availability as it involves locking entries during the write operation. 


### DB Sharding
Sharding is  horizontal pertitioning. It is an optimization techniques in achiveing horizontal scalability in database

### Reference
- https://www.youtube.com/watch?v=yNrVexoEnFM&list=PLt4nG7RVVk1g_LutiJ8_LvE914rIE5z4u&index=3
- https://www.youtube.com/watch?v=hdxdhCpgYo8


**Horizontal partitioning**: It splits a homogeneous type of data into multiple databases. You can see that such an algorithm is easily generalizable. That’s why sharding can be implemented at either the application or database level. In many databases, sharding is a first-class concept, and the database knows how to store and retrieve data within a cluster. Almost all modern databases are natively sharded. Cassandra, HBase, HDFS, and MongoDB are popular distributed databases. 

**Vertical partitioning**: It is very domain specific. You draw a logical split within your application data, storing them in different databases. It is almost always implemented at the application level — a piece of code routing reads and writes to a designated database.


#### Advantage 
- Faster read operation, optimized query
- avoid single point of failure
- reduced latency
- distributed database across different region or phhylical location, hence benefit in case of disaster recovery.

#### Disadvantage
- Join operation among multiple shard for evaluating any query is expensive
- Choosing a sharding strategy which will allow distributing load across multiple shards is difficult to decide
- Shards are inflexible, alternative - use hieranchial sharding.
- Use consistent hashing to map a request to a given shard  
- ##### Hotspots 
  - Hotspots are a common problem — having uneven distribution of data and operations. Hotspots largely counteract the benefits of sharding.

#### Alternative to sharding 
- indexing on the database
- query optimization

#### Types of sharding
- Logical sharding: In Sharding, one’s data is broken into two or more smaller chunks, called logical shards.
- Physical sharding: The logical shards are then distributed across separate database nodes, referred to as physical shards.


##### Based on the location of shard locator service

- **Algorithimic sharding** 
  - In algorithmic sharding, the client can determine a given partition’s database without any help. A hash function is used to decide which shard to choose.
  - Advantage: The hash function is designed to split data among a fixed no of nodes. 
  - Disadvantage: Adding/Deleting a new shards could be very difficult. Use consistent hashing to avoid this.
  - Important point: The shard key must not be changed rapidly 
  - Example: Memcache DB
- **Dynamic sharding**
  -  In dynamic sharding, a separate locator service tracks the partitions amongst the nodes.

#### KEY Based sharding
Key based sharding is an algorithmic sharding technique where the data are divided among multiple shards based on some shard keys. Hash function is applied on the keys to route the client to a specific traffic. 

**Advantage**
  - Simple and easy to implement

**Disadvantage**
  - Adding/deleting  a new shard is difficult
  - The shard key should be choosen very carefully to avoid the need of resharding or remapping  entried to a different shard. For example, in an application where the user's current location is an important parameter and needs to be changed everytime an user relocates, `location` should not be used as the shard key.
  - Also some applications like tinder where clustering is around the `location` property, `location` should be used to make query faster.

#### Range based sharding
Range based sharding is an algorithmic sharding technique where the data are divided among multiple shards based on the range of values of a given shard key. 

**Advantage:** 
  - The database schema could be exactly same for all the shards
  - The range based shardiing does not rely on the has function. Hence, adding or removing a shard is not difficult. Range based sharding is useful when queries (a set of data needs to fetched) are based on certain range of values of a field (e.g. price range).
  -  The client application is resposible to maintain the mapping.

**Disadvantage**
  - It can lead to `hotspot` depending on how you shared the data. For example, if shopping db is sharded based on month, and if the some months are having more sell than others, then it will lead to poor distribution of data accross shards. This could be addressed by creating index on some key on the `hotspot` shard or creating a second layer of sharding. 

#### Directory based sharding 
Directory based sharding is a dynamic sharding technique. In directory based sharding, there exists a locator which holds the mapping of data to shard. The database is splitted up into multiple shards based on the value of a shard key. (e.g. country).
**It is a dynamic sharding strategy**

**Advantage**: 
- Addding or removing shards is very easy

**Disadvantage**
- The locator is a single point of failure, Therefore, you need to scale the locator to maintain availability.
- The locator adds one extra hop for the client in accessing the database. Hence, this increases the latency.
- Shard key must be choosen in such way that database is evenly distributed. Otherwise, it can lead to hotspot issue. 


## Indexing

### Objective
Making query faster

### Indexing in NO/SQl database
- Indexing are done on a sort key which is a combination of some keys present in that db
- Indexing improves read performance, but increases one more step (updating the indexes) in write operation.
- Index Scan v/s Index Only scan (avoid reading data from disk)
- https://www.youtube.com/watch?v=mTNkqMDCasI
- https://www.youtube.com/watch?v=HaEPXoXVf2k


## Optimizing write to the database
TODO

