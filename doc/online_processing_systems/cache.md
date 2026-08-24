For a read operation if the data is present in the cache then the request will be serviced very fast. If the system has a good cache hit percentage then the throughput (number of requests serviced per second) increases.  
When an app tier gets a read request, it first tries to get the value from the cache, a good cache implementation will have 95-98 hit percenrage, if the request key is not present in the cache, then the app gets the value from the database, it also puts the value in the cache.  
For write and upgrade requests, data will be updated in the DB and the entry will be removed from the cache to invalidate the cache.  
For the delete requests, data can be deleted both from the DB and the cache.  
When the cache is full, it uses its eviction policy to remove a item to accommodate the new entry. Least recently used eviction policy works best, this uses the principle of temporal locality.

# Least recently used eviction implementation(LRU)
In addtion to the hash map for storing the key value pairs of the cache, LRU can use a min heap based on timestamps to keep track of the least recently used key, but updating the min heap after each read will take O(logN) time, thus the overall cache complexity increases to O(logN). 
The most efficient solution that is used to implement LRU is to inject double linked list structure into the main hash map by adding previous and next nodes into the hasp map's value. This double linked list should have the latest used items(key-value-prev-next) at the front and least recently used items at the end. In this case adding an item at the front for a new key and deleting an item at the end of the list when the cache is full can be done in a contant time. Search also can be done in constant time using the main hash map data structure. The space complexity of this data structure is O(n). Below is the sample code.

```c++

//Initialization
int capacity; //Stores the maximum cache capacity
int curr_size; //Stores # key-value pairs(in hash map)
unordered_map<int, node*> hmap; //Start with a empty hash map
node *head = nullptr;
node *tail = nullptr;

//Node and hashmap value
class node
{
    int key;
    int val;
    node *prev;
    node *next;
};

//get function, should return the value associated with the given key
int get(int key)
{
    //if key not in hasp map, it is a cache miss
    if(!hmap.contains(key))
    {
        return -1;
    }

    //else key exists in the cache, cache hit
    //first move the key's node to the head of the linked list
    extract_and_push(key);

    return head.val;
}

//put function, if key already present in the hash map then it is a update operation else it is a insert operation
void put(int key, int value)
{
    //update
    if(hmap.contains(key))
    {
        hmap[key]->val = value;

        //then move this key's node to the head of the linked list
        extract_and_push(key);

        return;
    }

    //insert
    //if cache is full, purge out the LRU node(both from hashmap and linked list)
    if(curr_size == capacity)
    {
        //remove the tail from the hashmap
        hmap.erase(tail.key);

        //remove the tail from the linked list
        node* penultimate = tail->prev;
        penultimate->next = nullptr;
        delete tail;
        tail = penultimate;
        curr_size--;
    }

    //insert the new key, value pair
    if(head == nullptr) //empty linked list
    {
        head = new node(key, value);
        tail = head;
    }
    else
    {
        node* new_node = new node(key, value);
        new_node->next = head;
        head->prev = new_node;
        head = new_node;
    }
    hmap[key] = head;
    curr_size++;
}

//function to extract the key node and push it at the head of the doubly-linked list
void extract_and_push(int key)
{
    node* ex_node = hmap[key];

    //if the node is already at the head position, nothing to do
    if(ex_node == head)
    {
        return;
    }

    node* prev_node = ex_node->prev;
    node* next_node = ex_node->next;
    prev_node->next = next_node;
    if(next_node)
    {
        next_node->prev = prev_node;
    }
    else //when we are extracting the tail node, next_node is null
    {
        tail = prev_node;
    }

    //push the node as the new head
    ex_node->next = head;
    ex_node->prev = nullptr;
    head->prev = ex_node;
    head = ex_node;
}

```

# Cache tier strategies
There is no need for the cache to directly communicate with the DB, the app tier can communicate with both DB and cache tier and control the flow, this setup is known as cache-aside architecture.  
Like the DB we can replicate the cache tier using leader and leaderless schemes to keep the throughput and availability high.  
Another setup is where the app tier only communicates with the cache, the cache then communicates with the DB for a miss, this is known as read-through/write-through architecture.  In the same setup if we don't update the DB immediately on write, but write to the DB periodically or when the item needs to be evicted from the cache, this strategy is called write-back or write-behind strategy.