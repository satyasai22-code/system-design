# Caching

- Caching is everywhere(CPU, browser)

Pros:
- Improve read performance(aka Latency): Memory access >> Disk access >> Network Access
- Reduce the load(aka Throughput): 
   Eg: Database and Cache. If result is found in cache the db will have 1 less request to process.

Cons:
- Increases complexity
- May result into data inconsistency
- Consumes resources

App Layer -> Cache layer -> Storage layer

# Caching Strategies
 **Cache Aside**
   - Most common strategy
   - Check key in cache. If not then directly (app layer) fetch from DB and update the cache
   
   - Pros: 
     - cache only what data needed  
   - Cons:
     - Cache misses are expensive
     - Data staleness(old data when compared to storage)
     - Implementation complexity(Devs need to have apis for both cache and db)

**Read Through**
   - Application doesn't hv direct access to storage(db) but interacts with cache api
   - In case of cache miss, cache api layer(not the app layer) fetches from storage, update it in cache and return to the app.
   - Quite used in ORM
   - Pros:
     - Cache ony whats needed
     - Transparent(dev only knows one api(cache layer api))
   - Cons:
     - Cache misses are expensive
     - Data staleness
     - Reliability

- **Write Through**
   - Similar like Read through but releated to update than fetch
   - App layer updates cache layer and then cache layer updates storage layer
   - No data staleness as each update pass through cache layer before storage layer
   - Pros:
     - Up-to-date data(No data staleness)
   - Cons:
     - Writes are expensive(as we update in both cache and storage layer each time)
     - Redundant data(Might write data to cache that no one might ever read it)

**Write Behind**
  - Similar like Write Behind 
  - Only difference is that instead of writing to storage layer each time, it waits for more writes(bulk) or a timeout before writing to storage layer.
  - Here cache acts as a buffer
  - Pros:
    - No write Penalty(For user it looks faster as we dont write to storage every time)
    - Reduced load on storage(As we update Storage in bulk)
  - Cons:
    - Reliability(If crash we may lose data)
    - Lack of consistency(If we dont flush from buffer often enough)

  App Layer -(Write to storage) > Cache Layer -(Update Cache)> Wait for more writes or timeout > write to storage > Storage Layer

# Eviction Policies
### LRU(Least recently used): 
- Default algorithm for cache
- A Linked list where head points to next element to be removed(Tail represent most recently used and head represents least recently used)
- But more frequently accessed element may be evicted
### LFU(Least Frequently Used):
- Every key has a counter incremented each time it is accessed
- Keys often accessed stay in the cache
- It has an overhead of keeping counter for each key
- Also sometimes a key may be accessed often in the past but not anymore will stay in the cache for a long time 
- Its a tradeoff
- LFU suffers less from false cachce eviction
- Implementation: https://www.codingninjas.com/studio/library/least-frequently-used-cache

# Redis
- In Memory(Single instance of redis may not store more data. calculate overall RAM you need and divide it by some num(say 16GB) to get count of instances)
- As All data is in memory it has extremely very high throughput compared to RDBMS 
- Key - value
- Limited by RAM(Biggest are 500GB RAM)
- Supports 100k+ requests/sec on single node
- has TTL(Time to Live) mechanism(After TTL the key wont be found in cache)
- TTL is very imp otherwise cache runs out of memory
- supports persistence(saves from RAM to DISK. Imp for Disaster Recovery)
- If persistence is supported then why not Redis as db? No. Redis cant be used as DB as redis persists data into disk for every 1 second(1 sec(not milli sec or micro sec) for better performance). In case of crash, we lose data of last second
- Redis is a remote server. So it comes with network latency and added complexity. Egs: What should we do when we cant connect to Redis? In case of crash can we work with db or other services in the mean time?
### Data Types
  - Supports: String, List, Set, Hash and some others
  - Not supported: JSON or nested data types 
# More Read 
 - https://medium.datadriveninvestor.com/all-things-caching-use-cases-benefits-strategies-choosing-a-caching-technology-exploring-fa6c1f2e93aa
 - https://en.wikipedia.org/wiki/Cache_replacement_policies