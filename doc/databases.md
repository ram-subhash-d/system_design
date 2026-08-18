# Key-value stores
Key value pairs is an important way to model data. The key should be a unique id, to uniquely search or update in the database. The value can be either uninterpreted sequence of bytes or well defined structured data like XML or JSON. For well defined structured data, the data validation like checking if it is in the right format or checking against a schema, will be done by the database. For uninterpreted sequence data(can be any string/data), data validation should be done by the application.  
If the value is well defined structured data, then we call such a store as document store, ex MangoDB.
There are also in-memory key-value stores like Redis and Memcache, which would be typically used in cache tier.
Each key-value collection is analoges to a table, multiple key-value collections is a database.

# Relational database
The most well known way of storing data is as a collection of structured n tuples. An n tuple is an extension of a key-value pair in which there are more than 2 fields. Each field/attribute will have a well defined type. We can visualize such a collection as a table/relation. Each row is called a record. The table definition, that is its name, its list of attributes and their types is called a schema. A collection of such structured tables/relations is called a relational database. Therefore these tables are highly structured and regid unlike the key-value stores.  
One of the attribute or a combination of attributes in the table can be made the key, whose combined value is unique. There can also be multiple keys in a table. We have to pick one particular key as the primary key. The database system will build an in-memory hash index on the primary key, like the key-value store here also the in-memory hash map will be on the primary key and the offset to the corresponding record on the disk.  
If the order is important, like if the name field is the primary key and we want to get all records with name starting with a particular letter(range query), then we go for in-memory balanced tree(tree index), instead of hash map and do a in-order traversal to get a range of records. The advantage of tree index is that it can also have duplicate keys, so tree index can be used for any attribute not just primary key. Depending on the use case the database can build multiple hash and tree indexes for faster access.

# SQL
For key-value stores we use the simple CURD API(insert(k,v), update(k,v), delete(k), search(k)), to query and update the database. But with relational databases we have a much more structure way of querying and manupulating the database, using a high level language called SQL(also called Structured Query Language or SEQUEL). Below are some of the example SQL commands.
CREATE TABLE Students(sid INTEGER, name CHAR(20), login CHAR(20), age INTEGER, gpa REAL, PRIMARY KEY(sid))
INSERT INTO Students VALUES (53688, "Smith", "Smith@cs", 18, 3.2)
DELETE FROM Students WHERE name="Smith"
UPDATE Students SET age=age+1,gpa=gpa-1 WHERE sid=53688
SELECT * FROM Students WHERE sid=53688  
The response of a SELECT command is another table.

# Foreign keys
In relational databases, tables need to be related, for example user table must be related to the region table which has details about the region where the users live. Each user must be linked to a region table through a region id field/attribute. This region id is a primary key in the region table and a foreign key in the user table. Every single region id entry in the user table must reference an region id entry in the region table.   
CREATE TABLE Users(region-id INTEGER, ..., PRIMARY KEY(user-id), FOREIGN KEY(region-id) REFERENCES regions)

# Normalization
Also in relational databases, there can be a lot of duplication, if the databases schema is not properly designed. In the above example if we combine user table and region table into one table, there will be a lot of duplication if many users belong to the same region, the act of removing duplicate information is called normalization.

# ACID Transactions
In relational databases since information is distributed across multiple tables, to get summarized information we need to join information from multiple tables. But join operations are costly.  
A sequence of DB operations, corresponding to a logical piece of work, that must be executed as a unit, in an all or none fashion is called a transaction.  
Example : Transfering money from one account to another
START TRANSACTION
UPDATE Account SET balance=balance-50 WHERE id="Omkar"
UPDATE Account SET balance=balance+50 WHERE id="Gaurav"
COMMIT  
A general transaction is expected to satisfy the below 4 properties
* Atomicity : The work of the transaction should be either fully completed or shouldn't be done at all.
* Consistency : If executed completely, the transaction takes the DB from one valid state to another valid state.
* Isolation :  If T1, T2, ..., Tn are trasactions submitted at the same time, then even though their execution is interleaved, for any Ti the effect is as if it ran as one unit(i.e completed before/started after every other transaction).
* Durability : When a transaction has successfully completed, its effect should be permanently recorded in the DB.

Atomicity and durability are ensured by the logging and recovery manager of the database system. Concurrency control manager is responsible to maintain isolation.

# Big data and NoSQL
In modern times as the amount of data exploded like web logs, social media, mobile apps, sensors etc. Companies need to keep track of these kinds of data to indentify trends. It became impossible to scale relational databases to handle these massive amounts of data. This new data, like web page contents, image files, audio, video, tweets, etc, also did not have a structure to use relational databases. This new data with high volume, high velocity and high variety is called big data.  
These big data applications did not need ACID properties or joins or do normalization or to save space.
So in modern time, we might choose to store user inform as a json file in a document store, instead of making a relational database from it. It will be much faster than relational database.
The document and key-value stores where optimized for performance and they let go of the relational model, they are known as NOSQL databases(did not support relational model and SQL language).  
New features where also added to relational databases so that they can scale to large amounts of data, these databases are known as NEWSQL(examples google cloud spanner, cockroach DB and yugabyte DB).