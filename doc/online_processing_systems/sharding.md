What if the data is very large that it cannot fit on a single machine or if the writes are so high that a single machine cannot handle. Then we go for sharding/partitioning, break up the data accross multiple machines.  

In this case each key-value pair belongs to one partition. We treat each partition as a seperate DB/cache. For availability each partion both DB and cache should also be replicated.  
number of shards(s) = total data size/space on a commodity server.

How do we assign a key-value pair(DB item) to a shard?
One way is to have hand curated rules to assign a key to a particular shard. For example if the key is a string then based on the starting character we can assign a partition to it. The problem with this is that if the hand curated rules are not carefully thought of, then some of the partions might end up getting a disprotionate amount of data or traffic. If a particular partition is getting a lot of traffic then we call it a hotspot. The target is to partition the data in such a way that none of the partitions become hotspots.  

Another way is to hash the key and map it to a partition based on the hashing result. Here the mapping is unpredictable but deterministic. The machine/application which assigns a key to a partition is called partition-aware load balancer/router.  
shard id = MD5(key) % s (where s is the total number of shards)

# Resharding
The above sharding technique does not work if we have to increase the number of shards dynamically over time as the data increases, this is because if we change s in the above formula, the shard id changes for a given key. This means all the data and queries need to be rehashed and moved from one shard server to another, this is called resharding and it is very expensive.

# Consistent hashing
Can we eliminate this resharding? yes, by using consistent hashing.  
Instead of hashing the key-value pairs directly to shard id's, we introduce virtual partitions(with id's) inbetween and hash the keys into these partition id's. Each shard server can now loadup multiple virtual partitions. The number of virtual partitions is fixed and never changing but the number of shard servers can increase(or decrease). Now the same hash function is applied to the shard servers, to map a server to a virtual partition id. All the virtual partitions left to this partition id till we hit another servers virtual partition id will be loaded into this server.  
If a new shard server is introduced, this new server will be mapped to a virtual partition id, only the partitions to the right of this id till the next servers partition should be moved to this new server, there is no need for a full resharding.  
These kind of setups will have a zookeeper along with the LB/router, the zookeeper is responsible to maintain a table on the LB to map virtual partition id's to shard servers, then there is a change in the number of shards, the zookeeper will move the data and update the table.