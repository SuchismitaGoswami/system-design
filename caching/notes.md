## Caching

### Important points to consider while choosing any cache
1. Features & Specs
2. Cache Access Pattern
3. Cache Eviction
4. Scalability


### Caching patterns 
- ![caching pattern](caching%20patterns.png)

### Cache replacement or cache eviction
- Least recently used
- Least frequently used
- Combination of both 

### Cache invalidation
  - TTL is used to mark invalidate the data in a cache after the expiration period.

### Distributed Cache
Q: Design a distributed cache
Ans:  
  - `Functional requirement`:
    - get item
    - put item
  - `Non functional requirement`
    - highly scalable
    - high performance
    - high availability

Design 
Background: 
Why do we need a cache?
![need_of_cache](./need_of_cache.PNG)

Simple: Step 1: 
The application service contains the cache in memory
In memory cache
![in_memory_cache](./in-memory-cache.png)

Cache Eviction strategy
Least recently used algorithm

Data structure to implement hash
- Hash map to keep the mapping
- Double linked list to keep the nodes with values and maintain priority in terms of its usage


#### Simple: Step 2:
Moving towards distributed world.

Step 1: Keep the cache cluster as a seperate process inside the machine application service is running. This is called co-located cache cluster.

Step 2: Split the cache into multiple shards to handle more request and improve the speed.

Disadvantage with this approach
- Optimizing the hardware not possible as we need to keep the cache in memory so it must be memory intensive.
-  Cache is scaled along with the application server.

Improvement: 

- Run the cache cluster in a seperate machine or hardware setup.
- It also allows other services to use the cache

#### Simple: Step 3

Q. How the cache client decides which shard to choose?

A. If we use key based hashing to find the shard, then we will map the every request to a shard based on some hash function. Adding or removing a shard will require moving data from one shard to another shard.

![key_based_sharding](./key_based_hashing.PNG)

Improvement:
Use consistent hashing

Q. Where the mapping of sharding will be maintained? or who will maintain that?

**Option 1**. Each application service will use a cache client to communicate to the cache shard. Now each client can maintain the mapping somewhere in a configuration file.

We can use configuration tool (e.g. chef) to deploy the latest change of the configuration file  to the application server when a new shard is added or some shard is deleted.

**Option 2**. There could be a configuration service (e.g. zookeeper) which will maintain the list. Each cache shard server will need to send heartbeat request to the configuration service to register itself in every certain interval.

![service_registry](./cache_server_registry.PNG)

High Performance?
A: yes, 
  1. Cache client communicate with cache shard via TCP or UDP
  2. Data are splitted into multiple shards to handle more request

High Availability?
A. No,
  1. In case one shard fails, it will increase load to the next shard as all the load handled by that shard will fall back to the nearest shard as per consistent hashing technique. We need to find some way to scale the shard or have multiple replications.

#### Simple: Step 4
We can follow master slave architecture to have multiple replications of every single shard.
 
Each shard will have a leader which will handle all the write request and will have multiple read replicas or slaves. If the leader fails, any of the slave becomes the new leader. 

**Now, who will do this monitoring?**

A `Configuration service` like zookeeper will do this task. 

![master_slave](./master_slave_cache_replication.PNG)

**How the replication will happen?**
Data replication will be handled asynchronously. To handle consistency issue, we need to use synchronous replication.

**Cache eviction**
- LRU Cache replaceement
- Set up data expiry and have a batch job to remove the expired data from the cache.


#### Securing cache
 - Encryption and decryption before storing or retrieving data from cache
 - Put cache behind a firewall to allow traffic from the authenticated server or client
 - Never put cache in the public subnet


#### Caching topologies
- Single in memory caching topology
- Distributed caching 
- Replicated caching
- Near cache hybrid 
  - Each application service will have a private cache in memory. The objective of a Near Cache is to provide the best of both worlds between the extreme performance of the Replicated Cache Service and the extreme scalability of the Partitioned Cache Service by providing fast read access to Most Recently Used (MRU) and Most Frequently Used (MFU) data. To achieve this, the Near Cache is an implementation that wraps two caches: a "front cache" and a "back cache" that automatically and transparently communicate with each other by using a read-through/write-through approach.

  - The "front cache" provides local cache access. It is assumed to be inexpensive, in that it is fast, and is limited in terms of size. The "back cache" can be a centralized or multi-tiered cache that can load-on-demand in case of local cache misses. The "back cache" is assumed to be complete and correct in that it has much higher capacity, but more expensive in terms of access speed. The use of a Near Cache is not confined to Coherence*Extend; it also works with TCMP.
  - https://docs.oracle.com/cd/E14526_01/coh.350/e14510/nearcache.htm#COHGS227
