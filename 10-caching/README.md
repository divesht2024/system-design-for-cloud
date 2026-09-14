# 10. Caching

## 1. What is Caching?

**Caching** means storing frequently accessed data in a faster storage location so that future requests can be served faster.

Instead of repeatedly fetching data from a slower source such as a database, the application first checks the cache.

```text
Without Cache:

User → Application → Database
```

With cache:

```text
User → Application → Cache
                    ↓
              Cache Hit → Response
                    ↓
              Cache Miss
                    ↓
                 Database
```

---

# 2. Why Do We Need Caching?

Caching helps to:

* Reduce response latency
* Reduce database load
* Increase application throughput
* Handle more users
* Improve application performance
* Reduce repeated expensive operations

### Simple Example

Suppose 10,000 users request the same product:

```text
Without Cache:

10,000 requests → Database
```

With cache:

```text
First request → Database → Cache

Remaining requests → Cache
```

This significantly reduces database queries.

---

# 3. Cache Hit

A **Cache Hit** occurs when the requested data is already present in the cache.

```text
User
 ↓
Application
 ↓
Cache
 ↓
Data Found
 ↓
Response
```

Example:

```text
GET /product/100

Cache → Product 100 exists
        ↓
      Return data
```

No database query is required.

### Memory

> **Cache Hit = Data found in cache**

---

# 4. Cache Miss

A **Cache Miss** occurs when requested data is not available in the cache.

```text
User
 ↓
Application
 ↓
Cache
 ↓
Data Not Found
 ↓
Database
 ↓
Store in Cache
 ↓
Response
```

Example:

```text
GET /product/100

Cache → Not found
          ↓
       Database
          ↓
      Store in Cache
          ↓
       Response
```

### Memory

> **Cache Miss = Data not found → fetch from source**

---

# 5. Basic Caching Architecture

```text
                  ┌──────────┐
                  │  Cache   │
                  └────┬─────┘
                       │
User → Load Balancer → Application
                       │
                       ↓
                   Database
```

A common flow is:

```text
Request
   ↓
Check Cache
   ↓
 ┌───────────────┐
 │               │
Hit             Miss
 │               │
 ↓               ↓
Response       Database
                 ↓
              Cache
                 ↓
              Response
```

---

# 6. Where Can We Use Caching?

### 1. Application Cache

Cache data close to the application.

Common technologies:

* Redis
* Memcached

```text
Application → Redis → Database
```

---

### 2. Browser Cache

Browsers can cache resources such as:

* Images
* CSS
* JavaScript
* Fonts

```text
Browser → Cached Resource
```

This avoids downloading the same resource repeatedly.

---

### 3. CDN Cache

CDNs cache content at geographically distributed edge locations.

```text
User → CDN Edge → Origin
```

If the content is already cached at the edge:

```text
User → CDN Edge → Response
```

---

### 4. Database Cache

Frequently accessed database data can also be cached to reduce expensive database operations.

---

# 7. What Should We Cache?

Good candidates are generally:

* Frequently requested data
* Data that doesn't change frequently
* Expensive database queries
* Product/catalog information
* API responses
* Session data
* Static content

Example:

```text
Product Catalog
    ↓
Frequently requested
    ↓
Good cache candidate
```

---

# 8. What Should We Be Careful About?

Caching introduces some challenges.

### Stale Data

The cache may contain old data.

```text
Database:
Price = ₹500

Cache:
Price = ₹450
```

The application may return stale information.

---

### Cache Invalidation

When underlying data changes, cached data may need to be:

* Updated
* Deleted
* Expired

A common problem is:

> **"How do we know when cached data is no longer valid?"**

---

### Cache Failure

If the cache becomes unavailable, the application may need to retrieve data directly from the database.

```text
Cache unavailable
      ↓
Application → Database
```

This can suddenly increase database load.

---

# 9. TTL — Time To Live

**TTL (Time To Live)** defines how long cached data should remain valid.

Example:

```text
Product data
TTL = 10 minutes
```

After 10 minutes:

```text
Cache Entry
    ↓
Expires
    ↓
Next request → Database
```

The application can then refresh the cache.

### Memory

> **TTL = How long cached data lives**

---

# 10. Caching vs Database

| Feature          | Cache                               | Database           |
| ---------------- | ----------------------------------- | ------------------ |
| Main purpose     | Fast access                         | Persistent storage |
| Speed            | Very fast                           | Slower than cache  |
| Data             | Often temporary/cached              | Primary data       |
| Persistence      | Depends on technology/configuration | Usually persistent |
| Typical examples | Redis, Memcached                    | MySQL, PostgreSQL  |

**Important:** A cache is generally **not a replacement for the primary database**.

---

# 11. Caching and Scalability

Caching can significantly improve scalability.

Without cache:

```text
1000 Requests
      ↓
1000 DB Queries
```

With cache:

```text
1000 Requests
      ↓
Cache
 ├── 950 Hits
 └── 50 Misses
        ↓
     Database
```

The database handles far fewer requests.

---

# 12. Cache Eviction

A cache has limited memory.

When it becomes full, some entries may need to be removed.

Common eviction concepts include:

* **LRU** — Least Recently Used
* **LFU** — Least Frequently Used
* **FIFO** — First In, First Out
* TTL-based expiration

For 0–2 YOE interviews, understand **LRU** at a basic level.

### LRU

Remove the data that hasn't been used for the longest time.

```text
Cache:

A B C D

A = recently used
B = recently used
C = old
D = oldest

New data arrives
      ↓
Remove D
```

---

# 13. Cache-Aside

**Cache-Aside** is one of the most common caching strategies.

```text
Application
    |
    ↓
 Check Cache
    |
 ┌──┴──┐
Hit   Miss
 |      |
 ↓      ↓
Return  Database
          ↓
       Update Cache
          ↓
        Return
```

The application is responsible for checking and updating the cache.

Example:

```text
GET product/100

1. Check Redis
2. If found → return
3. If not found → query DB
4. Store result in Redis
5. Return result
```

---

# 14. Cache-Aside Example

Suppose:

```text
Database:
Product 100 = Laptop
```

First request:

```text
Application → Cache
              ↓
             Miss
              ↓
           Database
              ↓
            Laptop
              ↓
          Store Cache
```

Second request:

```text
Application → Cache
              ↓
              Hit
              ↓
            Laptop
```

The database isn't queried for the second request.

---

# 15. Caching Policies

You should know these at a basic interview level:

| Strategy          | Main Idea                          |
| ----------------- | ---------------------------------- |
| **Cache-Aside**   | Application manages cache          |
| **Read-Through**  | Cache fetches data on read miss    |
| **Write-Through** | Write to cache and DB              |
| **Write-Back**    | Write cache first, DB later        |
| **Write-Around**  | Write directly to DB, bypass cache |

### Memory

```text
Cache-Aside  → App checks cache
Read-Through → Cache handles miss
Write-Through → Cache + DB
Write-Back    → Cache first, DB later
Write-Around  → DB directly
```

---

# 16. Real-World Architecture

A typical scalable application may look like:

```text
                    Users
                      |
                      ↓
                     CDN
                      |
                      ↓
                     ALB
                      |
              ┌───────┼───────┐
              ↓       ↓       ↓
            App 1   App 2   App 3
              \       |       /
               \      |      /
                    Redis
                      |
                      ↓
                   Database
```

Different caching layers can work together:

```text
Browser Cache
      ↓
CDN Cache
      ↓
Application Cache
      ↓
Database
```

---

# 17. Caching vs CDN

| Cache                                     | CDN                                  |
| ----------------------------------------- | ------------------------------------ |
| General caching concept                   | Distributed content delivery network |
| Can exist inside application architecture | Geographically distributed           |
| Often caches application/data             | Commonly caches HTTP/static content  |
| Redis/Memcached are examples              | CloudFront is an example             |

### Memory

> **Cache = Store data for faster access**
> **CDN = Store/deliver content closer to users**

---

# 18. Interview Questions

### Q1. What is caching?

Caching is storing frequently accessed data in a faster storage layer so that future requests can be served faster.

### Q2. What is a cache hit?

When requested data is found in the cache.

### Q3. What is a cache miss?

When requested data isn't found in the cache and must be fetched from the source.

### Q4. Why use caching?

To reduce latency, reduce database load, improve throughput, and increase scalability.

### Q5. What is TTL?

TTL defines how long a cache entry remains valid before it expires.

### Q6. What is cache invalidation?

The process of removing or updating cached data when it becomes stale or invalid.

### Q7. What is Cache-Aside?

The application checks the cache first. On a miss, it reads from the database and then updates the cache.

### Q8. What happens if the cache fails?

The application may fall back to the database, but this can significantly increase database load.

### Q9. What is LRU?

LRU stands for **Least Recently Used**. It removes the cache entry that has not been used for the longest time.

### Q10. Is cache a replacement for a database?

Generally, **no**. The database is the primary persistent data store, while the cache is used to provide faster access to frequently needed data.

---

# Key Takeaway

```text
Caching
   ↓
Store frequently accessed data
   ↓
Faster response
   ↓
Lower DB load
   ↓
Better scalability
```

### 🔥 Interview Memory

> **Cache Hit → Data found → Fast response**
> **Cache Miss → Fetch from DB → Store in cache**
> **TTL → Cache lifetime**
> **Cache Invalidation → Remove/update stale data**
> **LRU → Remove least recently used data**
> **Cache-Aside → Application manages cache**
