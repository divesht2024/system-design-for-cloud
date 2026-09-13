# Load Balancer

## 1. What is a Load Balancer?

A **Load Balancer** distributes incoming traffic across multiple backend servers or instances.

Instead of sending all traffic to one server, the Load Balancer distributes requests across multiple healthy servers.

```text
                    Users
                      │
                      ▼
              ┌──────────────┐
              │ Load Balancer│
              └───────┬──────┘
                      │
             ┌────────┼────────┐
             ▼        ▼        ▼
          Server 1  Server 2  Server 3
```

This helps improve:

* Availability
* Scalability
* Reliability
* Fault tolerance
* Application performance

AWS Elastic Load Balancing distributes traffic across targets such as EC2 instances, containers, and IP addresses, and performs health checks to route traffic only to healthy targets.

---

## 2. Why do we need a Load Balancer?

Suppose we have only one server:

```text
Users
  │
  ▼
Server
```

If the server receives too much traffic:

```text
10,000 Users
     │
     ▼
  Server
     │
     X
  Overloaded
```

The server can become slow or unavailable.

Instead:

```text
10,000 Users
      │
      ▼
 Load Balancer
      │
 ┌────┼────┐
 ▼    ▼    ▼
EC2  EC2  EC2
```

Traffic is distributed across multiple servers.

---

## 3. Main Responsibilities

A Load Balancer mainly performs:

### 1. Traffic Distribution

Distributes incoming requests across backend targets.

### 2. Health Checks

Checks whether backend servers are healthy.

### 3. High Availability

If one server fails, traffic can be sent to other healthy servers.

### 4. Scaling Support

Works with Auto Scaling so new instances can receive traffic.

### 5. Fault Tolerance

Prevents one failed server from taking down the entire application.

---

# 4. How Load Balancing Works

Basic flow:

```text
Client
   │
   │ Request
   ▼
Load Balancer
   │
   │ Select healthy target
   ▼
Backend Server
   │
   ▼
Response
   │
   ▼
Client
```

For example:

```text
Client → Load Balancer → EC2-1
                         EC2-2
                         EC2-3
```

The client generally communicates with the Load Balancer endpoint rather than directly choosing a backend instance.

---

# 5. Health Checks

A Load Balancer periodically checks whether registered targets are healthy.

Example:

```text
              Load Balancer
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
     EC2-1        EC2-2        EC2-3
    Healthy       Failed       Healthy
       │            X            │
       └────────────┼────────────┘
                    │
              Traffic only to
              healthy targets
```

If `EC2-2` becomes unhealthy:

```text
Load Balancer
    │
    ├── EC2-1 ✓
    ├── EC2-2 ✗
    └── EC2-3 ✓
```

New traffic is sent only to healthy targets.

AWS ELB health checks are configured for target groups and are used to determine whether traffic should be sent to registered targets.

---

# 6. Load Balancing Algorithms

A Load Balancer needs a method to decide which backend should receive traffic.

For your **0–2 YOE level**, know these at a basic level.

## 6.1 Round Robin

Requests are distributed one by one.

```text
Request 1 → Server 1
Request 2 → Server 2
Request 3 → Server 3
Request 4 → Server 1
Request 5 → Server 2
```

Memory:

> **Round Robin = one by one**

---

## 6.2 Weighted Round Robin

Servers receive traffic according to their assigned weights.

Example:

```text
Server 1 → Weight 3
Server 2 → Weight 1
```

Server 1 receives more traffic because it has a higher weight.

Memory:

> **Higher weight = more traffic**

---

## 6.3 Least Connections

Traffic is sent to the server with fewer active connections.

```text
Server 1 → 10 connections
Server 2 → 3 connections
Server 3 → 7 connections

New request → Server 2
```

Memory:

> **Least Connections = send to the least busy server**

---

## 6.4 IP Hash / Source Hash

The client's IP address is used to determine the backend.

```text
Client A → Server 1
Client B → Server 2
Client C → Server 3
```

The goal can be to keep the same client mapped to the same backend while the mapping remains stable.

Memory:

> **IP Hash = client IP influences server selection**

---

## 7. AWS Elastic Load Balancing

AWS provides multiple types of Load Balancers:

```text
AWS Elastic Load Balancing
│
├── Application Load Balancer (ALB)
├── Network Load Balancer (NLB)
├── Gateway Load Balancer (GWLB)
└── Classic Load Balancer (CLB)
```

For modern AWS architectures, focus mainly on:

* ALB
* NLB
* GWLB

AWS documentation identifies ALB, NLB, GWLB, and Classic Load Balancer as the ELB types.

---

# 8. Application Load Balancer (ALB)

ALB operates at **Layer 7 — Application Layer**.

It is mainly used for:

* HTTP
* HTTPS
* Web applications
* Microservices
* Content-based routing

Example:

```text
                 Client
                    │
                    ▼
                  ALB
                 /   \
                /     \
               ▼       ▼
        User Service  Order Service
```

ALB can make routing decisions based on HTTP request information.

For example:

```text
example.com/users/*   → User Service
example.com/orders/*  → Order Service
example.com/products/* → Product Service
```

AWS ALB supports content-based routing through listener rules.

---

# 9. Network Load Balancer (NLB)

NLB operates at **Layer 4 — Transport Layer**.

It supports:

* TCP
* TLS
* UDP
* QUIC

It is designed for high-performance, low-latency traffic.

```text
Client
  │
  ▼
 NLB
  │
  ├── Server 1
  ├── Server 2
  └── Server 3
```

Memory:

> **NLB = Layer 4 + high performance**

AWS documents NLB as a Layer 4 load balancer supporting TCP, TLS, UDP, and QUIC.

---

# 10. Gateway Load Balancer (GWLB)

GWLB is mainly used for deploying and scaling **network/security virtual appliances**.

Examples:

* Firewalls
* Intrusion detection/prevention systems
* Deep packet inspection appliances

```text
              Traffic
                 │
                 ▼
              GWLB
                 │
        ┌────────┼────────┐
        ▼        ▼        ▼
     Firewall Firewall Firewall
```

GWLB operates at **Layer 3** and is designed to distribute traffic across virtual appliances.

For your level, remember its purpose rather than its internals.

---

# 11. ALB vs NLB vs GWLB

| Feature      | ALB                       | NLB                         | GWLB                |
| ------------ | ------------------------- | --------------------------- | ------------------- |
| Layer        | L7                        | L4                          | L3                  |
| Main traffic | HTTP/HTTPS                | TCP/TLS/UDP/QUIC            | IP traffic          |
| Main use     | Web applications/APIs     | High-performance networking | Security appliances |
| Routing      | Content-based             | Network/transport based     | Network traffic     |
| Example      | `/orders` → Order Service | TCP application             | Firewall            |

### Easy memory

```text
ALB  → Application
NLB  → Network
GWLB → Gateway / Security Appliances
```

---

# 12. Important Load Balancer Components

For AWS ALB, understand these components:

```text
Load Balancer
     │
     ├── Listener
     │
     ├── Listener Rules
     │
     └── Target Group
              │
       ┌──────┼──────┐
       ▼      ▼      ▼
      EC2    EC2    EC2
```

## Listener

A listener checks for incoming connection requests on a configured protocol and port.

Example:

```text
HTTPS : 443
HTTP  : 80
```

---

## Listener Rules

Rules determine how requests should be routed.

Example:

```text
/users/*     → User Target Group
/orders/*    → Order Target Group
/products/*  → Product Target Group
```

---

## Target Group

A target group contains the backend targets that receive traffic.

Targets can include things such as:

* EC2 instances
* IP addresses
* Containers

AWS ALB uses listeners, listener rules, and target groups to route requests to registered targets.

---

# 13. Load Balancer with Auto Scaling

Load Balancer and Auto Scaling commonly work together.

```text
                    Users
                      │
                      ▼
                    ALB
                      │
             ┌────────┼────────┐
             ▼        ▼        ▼
            EC2      EC2      EC2
             │        │        │
             └────────┼────────┘
                      ▲
                      │
                Auto Scaling
```

Suppose traffic increases:

```text
Low Traffic

ALB
 │
 ├── EC2
 └── EC2
```

Auto Scaling launches another instance:

```text
High Traffic

ALB
 │
 ├── EC2
 ├── EC2
 └── EC2 ← New instance
```

The Load Balancer can then distribute traffic across the available healthy targets.

---

# 14. Load Balancer Across Availability Zones

For high availability, the Load Balancer can operate across multiple Availability Zones.

```text
                    Users
                      │
                      ▼
                    ALB
                 /        \
                /          \
               ▼            ▼
             AZ-1          AZ-2
              │              │
          ┌───┴───┐      ┌───┴───┐
          ▼       ▼      ▼       ▼
         EC2     EC2    EC2     EC2
```

If an instance or Availability Zone has a problem, healthy targets can continue serving traffic.

AWS recommends configuring load balancer subnets across Availability Zones for high availability.

---

# 15. Load Balancer vs API Gateway

This is an important interview question.

| API Gateway                           | Load Balancer                        |
| ------------------------------------- | ------------------------------------ |
| API/service entry point               | Traffic distribution                 |
| Routes API requests                   | Routes traffic to targets            |
| Authentication/authorization features | Health checks                        |
| Rate limiting/throttling              | Distributes traffic                  |
| API management                        | High availability                    |
| Works at API/service level            | Often works at instance/target level |

### Easy memory

> **API Gateway → Which service?**

> **Load Balancer → Which instance?**

Example:

```text
Client
  │
  ▼
API Gateway
  │
  │ /orders
  ▼
Order Service
  │
  ▼
Load Balancer
  │
  ├── EC2-1
  ├── EC2-2
  └── EC2-3
```

---

# 16. What happens when a server fails?

Example:

```text
ALB
 │
 ├── EC2-1 ✓
 ├── EC2-2 ✗
 └── EC2-3 ✓
```

The Load Balancer detects that `EC2-2` is unhealthy through health checks.

New requests are sent to healthy targets:

```text
Request
   │
   ▼
  ALB
 /   \
▼     ▼
EC2-1 EC2-3
```

If Auto Scaling is configured, the failed instance can also be replaced.

---

# 17. Advantages

### Load Balancers provide:

* High availability
* Traffic distribution
* Fault tolerance
* Health checks
* Horizontal scaling support
* Better resource utilization
* Reduced dependency on a single backend server

---

# 18. Disadvantages

Load Balancers also introduce some complexity.

### Possible disadvantages:

* Additional infrastructure
* Additional cost
* Configuration complexity
* Possible latency from the additional network hop
* Requires monitoring and troubleshooting

---

# 19. Simple Real-World Example

Imagine an e-commerce application.

```text
                       Users
                         │
                         ▼
                       ALB
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
           EC2-1       EC2-2      EC2-3
              │          │          │
              └──────────┼──────────┘
                         │
                        RDS
```

During normal traffic:

```text
ALB → EC2-1
ALB → EC2-2
ALB → EC2-3
```

If EC2-2 fails:

```text
ALB → EC2-1
ALB → EC2-3
```

If traffic increases:

```text
Auto Scaling
     │
     ▼
New EC2 instance
     │
     ▼
Target Group
     │
     ▼
ALB sends traffic to it
```

This provides a scalable and highly available application architecture.

---

# 20. Interview Questions

### Q1. What is a Load Balancer?

**Answer:**

> A Load Balancer distributes incoming traffic across multiple backend servers or targets. It improves availability, scalability, and fault tolerance.

---

### Q2. Why do we need a Load Balancer?

**Answer:**

> To distribute traffic across multiple servers, prevent a single server from becoming overloaded, and continue serving traffic when a backend server becomes unhealthy.

---

### Q3. What is a health check?

**Answer:**

> A health check is a periodic check performed by the Load Balancer to determine whether a backend target is healthy and able to receive traffic.

---

### Q4. What happens if one EC2 instance fails?

**Answer:**

> The Load Balancer detects the failed instance through health checks and stops sending new traffic to it. Traffic is routed to healthy instances.

---

### Q5. What is ALB?

**Answer:**

> Application Load Balancer is an AWS Layer 7 load balancer designed mainly for HTTP and HTTPS traffic. It supports content-based routing using listener rules.

---

### Q6. What is NLB?

**Answer:**

> Network Load Balancer is an AWS Layer 4 load balancer designed for high-performance network traffic such as TCP, TLS, UDP, and QUIC.

---

### Q7. ALB vs NLB?

**Answer:**

> ALB works at Layer 7 and is mainly used for HTTP/HTTPS applications and content-based routing. NLB works at Layer 4 and is designed for high-performance network traffic.

---

### Q8. What is a Target Group?

**Answer:**

> A Target Group is a logical group of backend targets, such as EC2 instances, IP addresses, or containers, that receive traffic from the Load Balancer.

---

### Q9. What is a Listener?

**Answer:**

> A Listener accepts incoming connections on a configured protocol and port and uses listener rules to determine how traffic should be routed.

---

### Q10. Which load balancing algorithms should I know?

**Answer:**

> At a basic level: Round Robin, Weighted Round Robin, Least Connections, and IP Hash/Source Hash.

---

### Q11. Can API Gateway and Load Balancer be used together?

**Answer:**

> Yes. API Gateway can route a request to the appropriate backend service, while a Load Balancer can distribute that service's traffic across multiple healthy instances.

---

# 21. Interview Memory

Remember this flow:

```text
CLIENT
   ↓
LOAD BALANCER
   ↓
HEALTH CHECK
   ↓
SELECT HEALTHY TARGET
   ↓
BACKEND SERVER
   ↓
RESPONSE
```

### AWS memory:

```text
ALB  → L7 → HTTP/HTTPS → Application
NLB  → L4 → TCP/TLS/UDP/QUIC → Network
GWLB → L3 → Security Appliances
```

### Components:

```text
Load Balancer
      ↓
  Listener
      ↓
Listener Rules
      ↓
 Target Group
      ↓
EC2 / IP / Containers
```

### One-line interview answer

> **A Load Balancer distributes incoming traffic across healthy backend targets to improve availability, scalability, and fault tolerance.**

---

# 22. Interview Focus — 0–2 YOE

You should be comfortable with:

* What is a Load Balancer?
* Why do we need it?
* How does traffic distribution work?
* Health checks
* Round Robin
* Least Connections
* Basic IP Hash
* ALB vs NLB
* Basic GWLB purpose
* Layer 4 vs Layer 7
* Listener
* Listener Rules
* Target Groups
* Load Balancer + Auto Scaling
* Load Balancer across Availability Zones
* API Gateway vs Load Balancer
* What happens when an instance fails?

**Do not go deep into advanced load-balancing internals at this stage.**
