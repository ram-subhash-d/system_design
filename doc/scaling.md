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

We will also need a load balancer to send the request to the right machine based on partitions.

# Scaling for throughput
This is the scaling needed to handle the traffic that is comming in into the server. For this scaling is required on all 3 tiers, app, cache and DB. If we don't have existing data or a system to test on, we can do a rough estimation on the number of machines needed in each tier as follows.
* Let x = time(in ms), needed to process a given request, by a single thread on a specific machine(server). Then the thread can handle 1000/x requests per second. 
* If we assume 8-12 cores, 8 hyper-threads per core, then a machine will have 100 threads that can run in parallel. Thus the system can service 1000/x*100 requests. 
* But the server also needs some threads for running os and other things. For optimal utilization we have to keep the CPU utilization at 30-40%, that means the system can service 30000/x requests/second.
* Based on this we can find the number of systems needed in each tier.
Thus each layer must be a distributed system with load balancer to scale at that layer.

# Metrics used to measure the performance of a scalable system
We use service level indicators(SLI's), to quantitatively measure the level of service being provided. These are
* Correctness : Is the right answer/data being returned? It is determined by the logic of the application code. In some cases, where fews errors can be tolerated, we can define error rate as a fraction of all the requests. Correctness would be 1 - error rate.
* Availability : Could we respond to the request? Fraction of time that a service is usable, that is the fraction of valid requests that succeed in a given time.
* Throughput : Number of requests per second, that could be handled.
* Reponse time : How long it took to return a response to the client request. Studies show that if the response time is greater than 300 ms, then the user is going to feel it. We should consider average, mean or other such measures for response times over a given period.

# Service level objective(SLO)
An SLI should have a target value/range, this is called the service level objective. For example if we take the response time SLI, we want the response time to be within a certain target value, say response time <= 100ms, this kind of performance requirement for an SLI is called SLO.  
Functional requirements describe SLO's for correctness. Non-functional requirements describe SLO's for availability, throughput and response time.  
Service level agreement (SLA) : An explicit/implicit contract with your users on what the SLO's are, including the consequences of meeting/missing the SLO's. 

# Latency vs response time
Latency is the duration that a request is 'latent', i.e awaiting service, not being actually served.  
Response time  = latency + service time  
Latency includes the time the request takes to reach the server on the network + the time the response takes to reach the client on the network. Therefore it is also called round trip time. It is also a measure of the network performance.  
Another important performance metric for network is bandwidth(data rate), maximum number of bits that can be transmitted over the network per second, and network throughput is the actual data rate of the network at any given time.  
There are 3 components to latency on each link in the network
* Transmission time : It is the time taken by the client or router to transmit the meessage on to the next link, client/router can at max push the data at the network bandwidth rate.  
Transmission time = size of message/bandwidth
* Propagation delay : Time taken for the bits to travel on the link.  
Propagation delay = distance/speed of light
* Queuing delay : As the message is going accross the network, it get stored at each router in a queue along the path, before being forwarded to the next router, this is known as the queuing delay.  
So the total latency is the cumulative sum of these 3 components accross each link over the full network path. When data to be sent is large like a photo/video then the transmission time dominates, which can be improved by increasing the network bandwidth. When data is small then the propagation delay will dominate, to improve this we can add a additional server close to the client to decrease distance.