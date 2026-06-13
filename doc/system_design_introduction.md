# Client server model
In a client server model, server is always running waiting for the next request. It is black box, where the outer world knows it only through its API, while keeping the implementation private. The server is made up of micro services which interact with each other to provide the server's functionality. The client on the other hand is a temporary connection to the server which uses the server's API by request and response.  

# System
A system is a set of components which interact with each other to provide a overall functionality at its interface level, like a black box again. Everything outside the system is called the environment. At the base level each component is composed of 3 atomic units
* CPU : Compute units, ability to execute instructions.
* Memory : RAM or disk, storage unit, read and write data.
* Communication : Which link the CPU's and the memory units(bus/network), sends data between units.
These units can be present accross many computers not just one, the entire set is called the system. If the units are present accross multiple computers then it is called a distributed system.  

If the system can handle large number of requests with good response time, then it is called a scalable system. First we build a normal micro service from the requirements and then later scale it.

# Functional requirements
These specify the overall functionality of the system. We are not worried yet about things like scalability, performance etc. This is the first thing that has to be done for system design, unpack the problem into functional requirements. Functional requirements must be written from the users use of the system perspective.

# Depth vs breadth orientation
If the system can be implemented with 1 micro service with all the details in it, it is called a depth oriented system, if the system is implemented with many micro services collaborating then it is called a breadth oriented system.

# Steps in system design
1. Gather functional requirements
2. Cluster them into a collection of micro services
3. Architecture diagram connecting them
4. Dive into each microservice
    * Draw the anatony assuming scale is not yet in the picture
    * Gather non-functional/capacity/scaling requirements and  
      check whether and how to scale each tier in a micro service
    * Propose a distributed system for each tier

# Reasons we may want to go for a distributed system
1. Check how much data needs to be stored : may need to scale DB and cache tier if size of data is too huge.
2. If the number of requests per second is too huge, need to scale for throughput, applies for all 3 layers.
3. If the reponse time is too high, need to parallelize the computation(multiple cpu's)
4. Availability/Reliability in the face of faults
5. Geolocation : Minimize network latency by using multiple servers at different locations
6. Hotspots : Disproportionately high load on a piece of data, have seperate set of machines(servers) to handle popular data.

# Verticle scaling
Also called scaling up or shared memory architecture. This is the simplest method of scaling, here we do not go for distributed systems. Instead we go for a more powerful machine, machine with many cpu's, many RAM's and many disks. Any CPU on the system can access any RAM or disk, hence called shared memory architecture.  
If the system needs to scale masively then verticle scaling is not feasible.

# Horizontal scaling
Also called scaling out or shared nothing architecture. Here we use distributed systems, increase the number of machines to handle load.