# 07. Scaling Fundamentals — Vertical & Horizontal Scaling

## 1. What is Scaling?

**Scaling** means increasing or decreasing system resources to handle changes in workload or traffic while maintaining performance and availability.

Example:

```text
Low Traffic
    ↓
2 Servers

High Traffic
    ↓
5 Servers
```

The goal is to make the system capable of handling increasing workload.

---

# 2. Why Do We Need Scaling?

As users and traffic increase:

```text
More Users
    ↓
More Requests
    ↓
More CPU / Memory / Network Usage
    ↓
Performance Degrades
```

Scaling helps the system:

* Handle more users
* Maintain performance
* Improve availability
* Handle traffic spikes
* Use resources efficiently

---

# 3. Vertical Scaling

**Vertical Scaling = Scale Up / Scale Down**

Increase or decrease the resources of an existing server.

```text
Before:

Server
2 CPU
4 GB RAM

        ↓ Scale Up

Server
8 CPU
32 GB RAM
```

### Example

An EC2 instance:

```text
t3.medium
   ↓
t3.2xlarge
```

The same server becomes more powerful.

### Advantages

* Simple to implement
* Usually requires fewer infrastructure changes
* Application may not need major architectural changes

### Disadvantages

* Hardware/resource limits
* Larger instances can become expensive
* May require downtime depending on the system
* Does not automatically remove the single-server failure problem

### Memory

> **Vertical = Bigger Server**

---

# 4. Horizontal Scaling

**Horizontal Scaling = Scale Out / Scale In**

Add or remove multiple servers.

```text
Before:

        Server 1


       Scale Out
           ↓

Server 1   Server 2   Server 3
```

### Example

```text
2 EC2 Instances
      ↓
5 EC2 Instances
```

Traffic is distributed across the instances using a load balancer.

```text
                ┌──→ EC2 1
Users → ALB ────┼──→ EC2 2
                └──→ EC2 3
```

### Advantages

* Handles large traffic
* Better availability
* Can tolerate individual server failures
* Works well with Auto Scaling
* Can scale by adding more machines

### Disadvantages

* More infrastructure complexity
* Requires traffic distribution
* Application may need to support multiple instances
* Distributed-system challenges can appear

### Memory

> **Horizontal = More Servers**

---

# 5. Vertical vs Horizontal Scaling

| Feature        | Vertical Scaling          | Horizontal Scaling             |
| -------------- | ------------------------- | ------------------------------ |
| Also called    | Scale Up/Down             | Scale Out/In                   |
| Method         | Increase server resources | Add/remove servers             |
| Example        | 4 GB → 32 GB RAM          | 2 → 5 servers                  |
| Complexity     | Lower                     | Higher                         |
| Resource limit | Yes                       | Can scale across more machines |
| Availability   | Limited improvement       | Better                         |
| Auto Scaling   | Less common               | Common                         |
| Best for       | Smaller/simple systems    | High-traffic systems           |

---

# 6. Scale Up vs Scale Out

Easy way to remember:

```text
Scale Up
   ↑
Bigger Server


Scale Out
   → → →
More Servers
```

### Scale Down

Reduce resources:

```text
8 CPU → 4 CPU
```

### Scale In

Remove servers:

```text
5 Servers → 2 Servers
```

---

# 7. AWS Horizontal Scaling

A common AWS architecture:

```text
                  Users
                    |
                    ↓
              Application
              Load Balancer
                    |
          ┌─────────┼─────────┐
          ↓         ↓         ↓
        EC2       EC2       EC2
          \         |         /
           \        |        /
              Auto Scaling
```

When traffic increases:

```text
High Traffic
     ↓
Auto Scaling detects demand
     ↓
New EC2 instances launched
     ↓
Load Balancer distributes traffic
```

When traffic decreases:

```text
Low Traffic
     ↓
Auto Scaling removes unnecessary instances
     ↓
Cost decreases
```

---

# 8. Scaling with Auto Scaling

**Auto Scaling** automatically adjusts the number of instances based on demand or configured policies.

Example:

```text
Minimum = 2
Desired = 2
Maximum = 5
```

Traffic increases:

```text
2 → 3 → 4 → 5 instances
```

Traffic decreases:

```text
5 → 4 → 3 → 2 instances
```

This is called **dynamic scaling**.

---

# 9. Scaling and Load Balancer

Horizontal scaling normally works together with a load balancer.

```text
                Users
                  |
                  ↓
                 ALB
                  |
        ┌─────────┼─────────┐
        ↓         ↓         ↓
       EC2       EC2       EC2
```

The load balancer distributes requests across available backend instances.

This prevents one server from receiving all the traffic.

---

# 10. Stateless Applications and Scaling

Horizontal scaling works best when application servers are **stateless**.

Example:

```text
Request 1 → Server A
Request 2 → Server B
Request 3 → Server C
```

The application should not depend on local server memory for important shared state.

Instead, shared data can be stored in systems such as:

```text
Database
Redis / Cache
Object Storage
```

This allows any server to handle a request.

---

# 11. Real-World Example

Suppose an application initially has:

```text
1 EC2
```

Traffic increases:

```text
1 EC2
  ↓
Performance decreases
```

Use horizontal scaling:

```text
             ALB
              |
       ┌──────┼──────┐
       ↓      ↓      ↓
     EC2-1  EC2-2  EC2-3
```

Now traffic is distributed across multiple servers.

If traffic increases further:

```text
3 → 4 → 5 EC2 instances
```

Auto Scaling can manage this automatically.

---

# 12. Vertical + Horizontal Scaling

Real systems can use both.

```text
                 ALB
                  |
        ┌─────────┼─────────┐
        ↓         ↓         ↓
     Large EC2  Large EC2  Large EC2
```

Each server can be vertically scaled, while the number of servers can be horizontally scaled.

This is called **hybrid scaling**.

---

# 13. Interview Questions

### Q1. What is scaling?

Scaling is increasing or decreasing system resources to handle changes in workload or traffic.

### Q2. What is vertical scaling?

Increasing or decreasing resources such as CPU and RAM of an existing server.

> **Vertical = Bigger machine**

### Q3. What is horizontal scaling?

Adding or removing servers to handle workload.

> **Horizontal = More machines**

### Q4. Which is better: vertical or horizontal scaling?

Neither is universally better.

For high-traffic and highly available systems, **horizontal scaling is generally preferred** because more instances can be added and individual instance failures can be tolerated.

### Q5. What is scale out?

Adding more servers.

### Q6. What is scale in?

Removing servers.

### Q7. What is Auto Scaling?

Automatically adding or removing resources based on demand or configured policies.

### Q8. Why is a Load Balancer used with horizontal scaling?

It distributes incoming traffic across multiple backend instances.

### Q9. Why are stateless applications easier to scale horizontally?

Because any application instance can handle a request without depending on session/state stored locally on another instance.

---

# Key Takeaway

```text
Vertical Scaling
= Bigger Server
= Scale Up / Down


Horizontal Scaling
= More Servers
= Scale Out / In
```

### 🔥 Interview Memory

> **Up/Down = Server Size**
> **Out/In = Number of Servers**
> **ALB = Distribute Traffic**
> **Auto Scaling = Automatically Add/Remove Servers**
