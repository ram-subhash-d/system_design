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