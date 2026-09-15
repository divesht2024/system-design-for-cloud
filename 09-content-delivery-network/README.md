
# CDN (Content Delivery Network)

## 1. What is a CDN?

**CDN (Content Delivery Network)** is a distributed network of servers located in different geographical locations that delivers content to users from a server close to them.

The main goal is to **reduce latency and improve performance**.

Examples:

* AWS CloudFront
* Cloudflare
* Akamai

---

## 2. Why do we need a CDN?

Without CDN:

```text
User (India)
      ↓
Server (USA)
      ↓
Response
```

The request travels a long distance, which can increase latency.

With CDN:

```text
User (India)
      ↓
CDN Edge Location (India)
      ↓
Origin Server
```

If the content is already cached at the edge, the CDN can respond without contacting the origin.

---

## 3. How CDN Works

Basic flow:

```text
              User
                ↓
              CDN
                ↓
        Is content cached?
          /            \
        YES             NO
         ↓               ↓
   Return content     Origin Server
                         ↓
                   Store in CDN
                         ↓
                    Return content
```

### Cache Hit

Content exists at the CDN edge.

```text
User → CDN → Cached Content
```

Fast response.

### Cache Miss

Content is not available at the edge.

```text
User → CDN → Origin → CDN → User
```

The CDN fetches the content from the origin and may cache it for future requests.

---

## 4. Important CDN Components

### Edge Location

A geographically distributed location where CDN content is cached and served.

### Origin

The original server/storage containing the actual content.

Examples:

* S3
* EC2
* Load Balancer
* Web server

### Cache

Stores frequently requested content at the edge.

### TTL

**Time To Live** defines how long content remains cached before it expires or needs revalidation.

---

## 5. What Content Does a CDN Cache?

Common examples:

* Images
* CSS
* JavaScript
* Videos
* HTML
* Static files
* Some API responses

CDNs are especially useful for **static and cacheable content**.

---

## 6. CDN vs Normal Server

### Without CDN

```text
Users
  ↓
Origin Server
```

Every user directly reaches the origin.

### With CDN

```text
                 CDN
              /   |   \
             ↓    ↓    ↓
          Edge  Edge  Edge
             \    |    /
              Origin
```

Users are served from nearby edge locations whenever possible.

---

## 7. Benefits of CDN

### Lower Latency

Content is served from a location closer to the user.

### Reduced Origin Load

Cached content doesn't need to be fetched from the origin every time.

### Better Scalability

The CDN can handle a large number of requests.

### Higher Availability

Distributed edge locations can continue serving cached content even when the origin is under heavy load or temporarily unavailable, depending on configuration and content freshness.

### Security

CDNs can provide features such as:

* DDoS protection
* WAF integration
* TLS/HTTPS
* Traffic filtering

---

# 8. AWS CloudFront

**AWS CloudFront** is AWS's CDN service.

Basic architecture:

```text
User
 ↓
Route 53
 ↓
CloudFront
 ↓
Origin
 ↓
S3 / ALB / EC2
```

Example:

```text
             User
               ↓
          CloudFront
          /         \
      Cache Hit    Cache Miss
         ↓             ↓
      Response       ALB
                       ↓
                     EC2
```

CloudFront can use different origins such as:

* S3
* Application Load Balancer
* EC2
* Other HTTP servers

---

# 9. CDN + S3 Architecture

A common AWS static website architecture:

```text
User
 ↓
Route 53
 ↓
CloudFront
 ↓
S3
```

### Why?

* S3 stores static files.
* CloudFront caches them at edge locations.
* Users receive content from nearby edges.
* Origin S3 receives fewer requests.

---

# 10. CDN + ALB Architecture

For a dynamic web application:

```text
User
 ↓
CloudFront
 ↓
ALB
 ↓
EC2 / ECS
 ↓
Database
```

CloudFront can cache suitable content while dynamic requests can be forwarded to the application origin.

---

# 11. Cache Invalidation

Suppose you update:

```text
logo.png
```

But users are still receiving the old cached version.

You can perform a **cache invalidation** to remove cached content before its normal expiration.

Example:

```text
Old Content
     ↓
CDN Cache
     ↓
Invalidate
     ↓
New Content fetched from Origin
```

---

# 12. CDN vs Cache

These are related but not the same.

**Cache:**

A general technique of storing frequently accessed data closer to where it is needed.

**CDN:**

A distributed system specifically designed to deliver content from geographically distributed edge locations.

```text
Cache = Concept
CDN   = Distributed Content Delivery System
```

---

# 13. CDN vs Load Balancer

| CDN                     | Load Balancer                 |
| ----------------------- | ----------------------------- |
| Delivers/caches content | Distributes requests          |
| Uses edge locations     | Uses backend targets          |
| Reduces latency         | Improves availability/scaling |
| Reduces origin traffic  | Distributes traffic           |
| Example: CloudFront     | Example: ALB                  |

They can be used together:

```text
User
 ↓
CloudFront
 ↓
ALB
 ↓
EC2
```

---

# 14. CDN Request Flow — Interview

When a user requests content:

```text
1. User sends request
        ↓
2. Request reaches CDN
        ↓
3. CDN checks cache
        ↓
4. Cache Hit?
     /       \
   YES        NO
    ↓          ↓
 Response    Origin
               ↓
          Get Content
               ↓
          Cache Content
               ↓
            Response
```

---

# 15. What Happens When Origin is Down?

If the requested content is already cached and still valid, the CDN may continue serving it.

If the content is **not cached** or cannot be served from cache, the CDN generally needs to reach the origin, so the request can fail if the origin is unavailable.

For stronger resilience, design the origin itself for **High Availability**.

---

# 16. Interview Questions

### Q1. What is CDN?

> CDN is a distributed network of servers that delivers content from locations closer to users, reducing latency and improving performance.

### Q2. Why use CDN?

> To reduce latency, reduce origin load, improve scalability, and efficiently deliver static or cacheable content.

### Q3. What is an edge location?

> An edge location is a geographically distributed location where CDN content can be cached and served to users.

### Q4. What is an origin?

> The origin is the original source of the content, such as S3, ALB, EC2, or another HTTP server.

### Q5. What is a cache hit?

> When the requested content is already available in the CDN cache.

### Q6. What is a cache miss?

> When the content is not available in the CDN cache, so the CDN retrieves it from the origin.

### Q7. What is TTL?

> TTL defines how long cached content remains valid before expiration or revalidation.

### Q8. CDN vs Load Balancer?

> CDN mainly improves content delivery and reduces latency through caching, while a load balancer distributes requests across backend servers.

### Q9. Can CDN work with an ALB?

> Yes. CloudFront can use an ALB as an origin.

### Q10. Can CDN work with S3?

> Yes. CloudFront can use S3 as an origin for static content.

---

# 17. Memory Trick

```text
CDN = Content Closer to User
```

Remember:

**User → Edge → Cache → Origin**

And:

```text
Cache Hit  → Serve from Edge
Cache Miss → Fetch from Origin → Cache → Serve
```

## 🔥 30-Second Interview Answer

> **"A CDN is a distributed network of edge servers used to deliver content closer to users. When a user requests content, the CDN first checks its cache. If there is a cache hit, it serves the content directly from the edge. If there is a cache miss, it fetches the content from the origin, returns it to the user, and can cache it for future requests. This reduces latency, decreases origin load, and improves scalability. In AWS, CloudFront can use S3, ALB, or other HTTP servers as origins."**

### Key Takeaway

```text
CDN
 ↓
Edge Locations
 ↓
Cache Content
 ↓
Serve User Quickly
 ↓
Reduce Origin Load
 ↓
Improve Performance + Scalability
```
