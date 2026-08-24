# Storing a hash map(dictionary) in database(disk)
We can save a hash map in a database table with fields key and value. But searching the records will be expensive as we need to go record by record and check the key. Database's have introduced what is called as, an hash index. Hash index is nothing but a new in-memeory table which also stores key-value pairs, where key is key and value is 'offset in data file to the record with that key'(location). So while searching the database uses the hash index, gets the offset for the key we are searching for and directly retrieves that record. Note that when we add a record into the database table, database also adds the corresponding entry to the hash index table.

# CRUD
In general an application does create, read, update and delete operations known as CRUD on the database. All these operations use the above hash indexing, for faster times.

In these create, update and delete are write operations and read is a read operation. For high read systems, we can replicate the databases on multiple systems for throughput and place a load balancer in front of them. The main replica is called the leader, others are called followers. Read operations can be sent to any one of the replicas, the write operations will be sent to the leader, which will send the change log to the followers to update their copy. This is called single leader replication.

One issue with this is that the reads may sometimes see stale data until replication has completed. One way to solve this is to lock the item being updated untill all replications are complete, reads on the item will have to wait during this time.

If we have to replicate data across multiple data centers(for reduced response time based on geolocation), then we use multi leader replication where each data center will have one leader, here write operation at one leader will have to send the change log to all machines at all data centers. Here we can syncronise time at all data centers and use UTC time to syncronize data.

# Leaderless replication
* When writing, send write request to all replicas, once a majority of them acknowledge the write, consider it successful.
* When reading, ask for all replica's and take the majority view.
* Also we can save the timestamp of the latest write, this way when there is conflict based on time we can identify the latest value.

# CAP theorem
Suppose data is replicated in 2 data centers and the network connection between the data centers is lost, the replica's within each data center are still connected. 
If there is only one leader in one of the data centers, now if a write request is sent to a data center that does not have the leader, since the leader is not reachable, write will fail, this means our availability will drop but the data would continue to be consistant accross the 2 data centers, this kind of system is called consistent when partitioned(CP).  
If the system is multi leader and each center has one leader, then the write will succeed but will not be propagated to the 2nd data center, here the system is available but data is not consistent accross the 2 data centers, this kind of system is called available when partitioned(AP).  
So in case of no connection between the 2 data centers, we can either choose consistancy or availabilbity, this is know as the CAP theorem.

# Content distribution networks
If reads on your application are far more than the writes, the we can use a single central data center that has the database, and all writes will be directed to that center. Remaining all data centers will have only the app + cache tier(called proxy cache), this will serve most popular reads(95% of them) very fast. Such a setup is called content distribution networks(CDNs). These kind of networks can be used for news or streaming content distribution accross the globe. Large companies like google, netflix build there own CDNs.

