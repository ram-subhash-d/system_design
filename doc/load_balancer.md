# Proxy
A proxy is an intermediary between the client and server. Proxy does not do much, it just forwards the request/response to the client/server. But In the clients/servers eye's it gets the credit for doing all the work.  
A proxy at the server side is called reverse proxy and the one on the client is called forward proxy.  
In case of a server application that is horizantally scaled, reverse proxy also does load balancing and encryption/decryption.

# Load balancer(LB)
A load balancer forwards the request to one of the horizantally scaled servers. It uses some of the well known policies to balance load on the servers like, round robin, pick a server with least number of active connections, least response time server etc.  
A LB also needs a passive backup load balancer, if the main LB fails the passive becomes active and takes over.  
For websites with very high traffic, multiple LB's can be used and the DNS server can pick a LB in a round robin fashion, this is called DNS-based load balancing.

Nginx is a well known open source software load balancer.

# Why use load balancer
1. Increase throughput : It increases the number of requests that the system can handle, due to horizantle scaling and load balancer.
2. Increase availabilty : No single point of failure as there will be multiple servers under LB. LB sends healthchecks/heartbeats to servers to check if they are active and running.

When there are multiple data centers with load balancers at each of them, then we can give the same IP address to all these LB's. Then the client can use IP anycast to send the request to the closest data center. Network routers use the shortest path algorithms to find the closest data center. Routers will dynamically send broadcast messages to build the adjacency list of the current network graph. This will reduce the response time, as we picking the closest data center.  This is called global load balancing. 
In ethernet unicast will send the request to a single machine with the given IP address, multicast will send the request to all the machines with the same given IP address in the network and anycast will send the request to only one machine with the given IP address in the network(amoung many systems with the same IP address in the network), usually the closest one. And broad cast will send the request to the machines in the network irrespective of thier IP addresses.