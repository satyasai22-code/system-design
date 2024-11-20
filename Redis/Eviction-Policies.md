# Redis Eviction Policies

### 1. **`noeviction` (default)**
- **Behavior**: Redis returns an error when the memory limit is reached, and you try to write more data.
- **Best Use Case**: When you want to strictly control memory usage and ensure no eviction happens. Usually, this is not ideal for production environments with caching requirements.

### 2. **`allkeys-lru` (Least Recently Used)**
- **Behavior**: Evicts the least recently used keys, regardless of whether they have an expiration time (`TTL`) or not.
- **Best Use Case**: Suitable for general caching use cases where you want to ensure that the most recently accessed data stays in memory and least-used data gets evicted. This is one of the most popular and effective policies for cache-like scenarios.

### 3. **`volatile-lru`**
- **Behavior**: Evicts the least recently used keys **with an expiration time (`TTL`)** only.
- **Best Use Case**: Use this when only keys with TTL should be evicted, and you want Redis to retain non-expiring keys. Good for caching systems where some keys must always stay in memory (i.e., without TTL), and the cache should clean out the less-used expiring entries.

### 4. **`allkeys-random`**
- **Behavior**: Evicts random keys, regardless of whether they have an expiration or not.
- **Best Use Case**: When eviction should happen randomly. Generally not recommended unless your access patterns are very random or unpredictable, making other policies ineffective.

### 5. **`volatile-random`**
- **Behavior**: Evicts random keys **with an expiration time (`TTL`)** only.
- **Best Use Case**: Use this when you want to randomly evict only expiring keys and not touch non-expiring data.

### 6. **`volatile-ttl`**
- **Behavior**: Evicts keys with the nearest expiration time (i.e., keys that are going to expire soon).
- **Best Use Case**: Best for use cases where keys are expiring regularly, and you want to prioritize evicting keys that are about to expire anyway. This can be useful when data with TTL should be removed in a predictable order.

