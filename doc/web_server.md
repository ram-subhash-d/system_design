# Parallelism
In order to service multiple requests at the same time, we can run mutiple copies of the server program, run them as seperate processes. But this means each instance will have its own data and the data will not be synced.  
We can use threads to service parallel requests, threads will have single shared data that is synced, server program is run as a single process that is multi threaded.  
Another way is to use multiple copies of the server program or a multi threaded program called the app server, but the data resides on a central database server.  
When using multi threading or a central database server, we have to maintain the integrity of data, by using syncronization techniques on the critical sections of the code. Lock/mutex is one of the syncronization techniques used to allow only one thread to enter critical section at a time.  
If we are using a central database, DBMS already has all the syncronizations implemented.  
The application server should be stateless, that way when a request comes in, it does not matter which thread or process serves it. Since all data resides in a central database or on a shared memory and all the threads/processes don't have a state, any thread can process the request.  
A stateless app server also means that we can scale the app server horizontally to handle more load by increasing the number threads or processes. This means we can add a load balancer on top of the app servers, and depending on the load of the servers the load balancer can direct a request to server as any server can handle the request.  
Each server should also provide a request queue for the requests to wait if all the threads are busy. When a thread becomes available it takes the request from the front of the queue and starts processing it. Similarly the reponses might also need to be queued up as there might not be enough bandwidth to send many responses at once.

# Web server
A web server software will provide most of the above framework for a server, except for what we need to do for that particular web application on the threads. Some popular web services written in different languages are
* Python : Django, Flask 
* Java : Tomcat
* Javascript : node.js;
* C# : .NET 
* Ruby : Rails  

The server code can be layered into UI, App and DB. The UI can get the data from App and wrap it in HTML for the response. This a 3 layered architecture, presentation, business logic and storage, probably present on 3 different servers.  
The DB data resides on the disk, read from the disk is an expensive operation(as disk seek takes time), so applications have an in memory version of the data, cache, for faster access, between the app server and the DB server. The cache layer can be its own server, which has the cache algorithm's implemented for maximum hits.  
The overall architecture of a generic micro service will have these 3 layers, the app tier, the cache tier and the DB tier. 