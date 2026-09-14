# 09. Distributed Systems Fundamentals

## 1. What is a Distributed System?

A **distributed system** is a collection of independent computers, servers, or services that communicate over a network and work together as a single system.

Instead of running everything on one server:

```text id="q4x0hk"
                Application
                    |
              Single Server
```

A distributed system uses multiple machines:

```text id="a8n4fz"
                  Application
                       |
          ┌────────────┼────────────┐
          ↓            ↓            ↓
       Server 1     Server 2     Server 3
```

The machines communicate with each other over a network.

---

# 2. Why Do We Need Distributed Systems?

As applications grow, one server may not be enough.

Distributed systems help achieve:

* **Scalability**
* **High Availability**
* **Fault Tolerance**
* **Better Performance**
* **Geographical Distribution**
* **Handling large workloads**

Example:

```text id="v4x7f2"
Millions of Users
       ↓
Load Balancer
       ↓
Multiple Servers
```

Instead of one machine handling all requests, workload is distributed across multiple machines.

---

# 3. Basic Distributed System Architecture

A simple example:

```text id="0v6q0p"
                  Users
                    |
                    ↓
             Load Balancer
                    |
          ┌─────────┼─────────┐
          ↓         ↓         ↓
       App 1      App 2      App 3
          |         |         |
          └─────────┼─────────┘
                    ↓
                Database
```

Each server performs part of the workload.

---

# 4. Distributed Systems vs Single Server

### Single Server

```text id="a1g8zq"
Users
  |
  ↓
Server
  |
  ↓
Database
```

If the server fails:

```text id="0gc8h3"
Server ❌
   ↓
Application unavailable
```

### Distributed System

```text id="9q7c2m"
              Load Balancer
                   |
          ┌────────┼────────┐
          ↓        ↓        ↓
        App 1    App 2    App 3
```

If App 2 fails:

```text id="3jqxk4"
App 2 ❌

App 1 ✅
App 3 ✅
```

Traffic can continue through healthy instances.

---

# 5. Important Characteristics

## 1. Multiple Nodes

The system contains multiple independent machines or services.

```text id="q3r8d1"
Node 1
Node 2
Node 3
```

---

## 2. Network Communication

Nodes communicate over a network.

```text id="j1w9ko"
Service A
    |
    | HTTP / TCP / Message
    ↓
Service B
```

Because communication happens over a network, failures and latency can occur.

---

## 3. Independent Failures

One component can fail while others continue working.

```text id="f4b2y9"
Service A ✅
Service B ❌
Service C ✅
```

The system should handle such failures gracefully.

---

# 6. Scalability

Distributed systems allow workloads to be distributed across multiple machines.

```text id="x7m2da"
Traffic increases
       ↓
Add more servers
       ↓
Handle more requests
```

This is **horizontal scaling**.

---

# 7. High Availability

High availability means keeping the application accessible even when some components fail.

Example:

```text id="8g0h5s"
             Load Balancer
                  |
        ┌─────────┼─────────┐
        ↓         ↓         ↓
      EC2-1     EC2-2     EC2-3
        ✅        ❌        ✅
```

The load balancer can stop sending traffic to the failed instance.

---

# 8. Fault Tolerance

**Fault tolerance** means the system can continue operating despite failures.

Example:

```text id="k6d1e8"
Database Replica 1 ❌
        ↓
Database Replica 2 ✅
        ↓
Continue serving
```

Fault tolerance is one of the major reasons for using distributed architectures.

---

# 9. Replication

**Replication** means maintaining copies of data or services on multiple nodes.

```text id="x9p5j2"
             Primary
                |
        ┌───────┴───────┐
        ↓               ↓
     Replica 1       Replica 2
```

Benefits:

* Higher availability
* Fault tolerance
* Can improve read scalability

---

# 10. Partitioning

**Partitioning** means splitting data or workload across multiple machines.

Example:

```text id="b8s1kc"
Users A–M → Server 1
Users N–Z → Server 2
```

Instead of one server storing everything, data is divided across multiple nodes.

Database partitioning can help with scalability.

---

# 11. Communication Between Services

Distributed systems commonly use:

### Synchronous communication

One service directly waits for another service.

```text id="5p3j4a"
Service A
   |
   ↓ HTTP Request
Service B
   |
   ↓ Response
Service A
```

### Asynchronous communication

A service sends a message and does not need to wait for immediate processing.

```text id="0z9h8c"
Service A
   |
   ↓
Message Queue
   |
   ↓
Service B
```

This can improve resilience and decouple services.

---

# 12. Challenges of Distributed Systems

Distributed systems provide scalability and availability, but introduce additional complexity.

### Network Failures

```text id="g3w6v1"
Service A  X  Service B
```

The network connection can fail.

### Latency

Communication between machines takes time.

### Timeouts

A service may take too long to respond.

### Duplicate Requests

A request may be retried and processed more than once.

### Data Consistency

Different nodes may temporarily have different versions of data.

### Distributed Debugging

Finding the root cause across many services can be difficult.

### Partial Failures

One component may fail while the rest of the system continues operating.

---

# 13. CAP Theorem

CAP Theorem is an important distributed-system concept.

It states that when a **network partition** occurs, a distributed system generally has to choose between:

* **Consistency**
* **Availability**

while supporting **Partition Tolerance**.

```text id="7y2h8m"
Network Partition
       ↓
   ┌───────┐
   ↓       ↓
  CP      AP
```

### CP

Prefer:

> Consistency + Partition Tolerance

### AP

Prefer:

> Availability + Partition Tolerance

---

# 14. Real-World Example

An e-commerce application can be distributed into multiple services:

```text id="3r5h8a"
                     Users
                       |
                       ↓
                  API Gateway
                       |
       ┌───────────────┼───────────────┐
       ↓               ↓               ↓
   User Service   Order Service   Payment Service
       |               |               |
      DB              DB              DB
```

Each service can:

* Run independently
* Scale independently
* Be deployed independently
* Fail independently

This is a common microservices-based distributed system.

---

# 15. Distributed System + Cloud

Cloud platforms make distributed architectures easier to build.

Example AWS architecture:

```text id="f7k3q2"
                    Users
                      |
                      ↓
                  CloudFront
                      |
                      ↓
                     ALB
                      |
             ┌────────┼────────┐
             ↓        ↓        ↓
            EC2      EC2      EC2
             |        |        |
             └────────┼────────┘
                      ↓
                   RDS
```

Additional components such as caching, queues, replicas, and multiple Availability Zones can improve scalability and resilience.

---

# 16. Important Terms

| Term                | Meaning                                |
| ------------------- | -------------------------------------- |
| **Node**            | Individual machine/server              |
| **Replication**     | Creating copies of data/service        |
| **Partitioning**    | Splitting data/workload                |
| **Scalability**     | Handle increasing workload             |
| **Availability**    | System remains accessible              |
| **Fault Tolerance** | Continue despite failures              |
| **Consistency**     | Nodes provide consistent data          |
| **Latency**         | Time taken for communication           |
| **Timeout**         | Maximum wait time                      |
| **Partial Failure** | Some components fail while others work |

---

# 17. Interview Questions

### Q1. What is a distributed system?

A distributed system is a collection of independent computers or services that communicate over a network and work together as a single system.

### Q2. Why do we use distributed systems?

To achieve scalability, high availability, fault tolerance, better performance, and handle large workloads.

### Q3. What is a node?

A node is an individual machine, server, VM, or computing component participating in the distributed system.

### Q4. What is replication?

Replication means maintaining copies of data or services across multiple nodes.

### Q5. What is partitioning?

Partitioning means dividing data or workload across multiple nodes.

### Q6. What are common challenges?

Network failures, latency, timeouts, consistency problems, duplicate requests, partial failures, and distributed debugging.

### Q7. What is fault tolerance?

The ability of a system to continue operating when some components fail.

### Q8. What is the difference between replication and partitioning?

**Replication = Copy the same data.**

**Partitioning = Split different data.**

```text id="a3k7p1"
Replication:
A B C
↓ ↓ ↓
Same Data


Partitioning:
A B C
↓ ↓ ↓
Different Data
```

### Q9. What is a partial failure?

A situation where one component fails while other components continue operating.

### Q10. Why is distributed-system troubleshooting difficult?

Because a request can travel across multiple services and networks, making failures, latency, and dependencies harder to trace.

---

# Key Takeaway

```text id="v9x4c2"
Distributed System
       ↓
Multiple Machines / Services
       ↓
Communicate over Network
       ↓
Work Together
       ↓
Scalability + Availability + Fault Tolerance
```

### 🔥 Interview Memory

> **Distributed System = Multiple nodes working together**

Remember the core concepts:

```text id="h6p2q8"
Replication   → Copy data
Partitioning  → Split data
Scaling       → Handle more workload
Availability  → Keep serving
Fault Tolerance → Survive failures
CAP           → Consistency vs Availability during partition
```
