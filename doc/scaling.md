# Reasons we may want to go for a distributed system
1. Check how much data needs to be stored : may need to scale DB and cache tier if size of data is too huge.
2. If the number of requests per second is too huge, need to scale for throughput, applies for all 3 layers.
3. If the reponse time is too high because of bulky processing, need to parallelize the computation(multiple cpu's).
4. Availability/Reliability in the face of faults, so replication is required.
5. Geolocation : Minimize network latency by using multiple servers at different locations
6. Hotspots : Disproportionately high load on a piece of data, have seperate set of machines(servers) to handle popular data.

# Verticle scaling
Also called scaling up or shared memory architecture. This is the simplest method of scaling, here we do not go for distributed systems. Instead we go for a more powerful machine, machine with many cpu's, many RAM's and many disks. Any CPU on the system can access any RAM or disk, hence called shared memory architecture.  
If the system needs to scale masively then verticle scaling is not feasible.

# Horizontal scaling
Also called scaling out or shared nothing architecture. Here we use distributed systems, increase the number of machines to handle load.

# Scaling for data
First we calculate the amount of data to be stored in the database, as per the applications and time lines. A good asumption would be to take 1-2 TB disk space per machine. So if the database size required for a application is 20 TB, then probably we can distribute the database tier onto 10 machines. This is known as partitioning/sharding of the database.  

To make them fail safe we need to replicate the partitioned servers, a minimum of 3. So 30 database servers are needed.

Now that we distributed the database tier, how much distribution is needed for cache tier. A good rule of thumb is if we want 90% hits then we have to keep 10% entries in the cache, if we want 98-99% hits then we have to keep 20-30% entries in the cache. So for 20 TB database, 20% would be 4 TB of main memory required for the cache tier. Typical high end machines will have 128 GB RAM. So for 4 TB that would be 30 partitions. With replicaiton factor of 3 that would be 90 machines for the cache tier.  

If we think that the assumptions are not correct, then we have to use the existing data and do some experimentation to get the numbers and percentages.