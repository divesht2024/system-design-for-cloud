# Monolith vs Microservices

## 1. Monolithic Architecture

A **monolithic architecture** is an application where multiple functionalities are developed, packaged, and deployed as a single unit.

### Example

```text
                    Monolithic Application
        ┌──────────────────────────────────────┐
        │                                      │
        │  User Management                     │
        │  Product Management                  │
        │  Order Management                    │
        │  Payment Management                  │
        │  Notification Management             │
        │                                      │
        └──────────────────┬───────────────────┘
                           │
                      Single Database
```

All major application components are part of the same deployable application.

### Characteristics

* Single application/deployment unit
* Usually one codebase
* Components are tightly coupled
* Components commonly share the same database
* Simple to develop and deploy initially
* Entire application is generally scaled together

### Advantages

* Simple architecture
* Easy to develop initially
* Easy to test and debug
* Simple deployment
* Simple communication between components
* Lower infrastructure and operational complexity

### Disadvantages

* Entire application may need to be deployed for a small change
* Difficult to scale only one component
* A failure in one part can affect the entire application
* Large codebase becomes difficult to maintain
* Tighter coupling between components
* Technology changes can be difficult

---

# 2. Microservices Architecture

**Microservices architecture** divides an application into small, independently deployable services. Each service is responsible for a specific business capability and communicates with other services through APIs or messaging.

### Example

```text
                         API Gateway
                              |
             ┌────────────────┼────────────────┐
             ↓                ↓                ↓
        User Service     Order Service    Payment Service
             |                |                |
          User DB          Order DB         Payment DB
```

Each service can be developed, deployed, and scaled independently.

### Characteristics

* Multiple independently deployable services
* Services are organized around business capabilities
* Services communicate over the network
* Services can be scaled independently
* Services can potentially use different technologies
* Each service should have clear ownership of its data

### Advantages

* Independent deployment
* Independent scaling
* Better fault isolation
* Smaller and focused codebases
* Teams can work independently
* Different technologies can be used when justified

### Disadvantages

* More infrastructure complexity
* Network communication introduces latency and failures
* Distributed debugging is harder
* Monitoring becomes more complex
* Data consistency becomes more difficult
* Deployment and testing become more complicated
* Requires good CI/CD and observability

---

# 3. Monolith vs Microservices

| Feature               | Monolith                   | Microservices                 |
| --------------------- | -------------------------- | ----------------------------- |
| Deployment            | Single unit                | Multiple independent services |
| Scaling               | Usually entire application | Individual services           |
| Codebase              | Usually one large codebase | Multiple smaller codebases    |
| Communication         | Mostly in-process          | Network/API/messaging         |
| Database              | Often shared               | Prefer clear data ownership   |
| Deployment complexity | Low                        | High                          |
| Infrastructure        | Simpler                    | More complex                  |
| Fault isolation       | Lower                      | Higher                        |
| Debugging             | Easier                     | More difficult                |
| Technology choice     | Usually unified            | Can vary by service           |
| Operational overhead  | Lower                      | Higher                        |

---

# 4. When to Use Monolith?

A monolith can be a good choice when:

* The application is small or simple
* The team is small
* Requirements are still changing
* Independent scaling is not required
* Simple deployment is important
* Operational complexity should remain low

**Important:** Microservices are not automatically better than monoliths. They introduce distributed-system complexity, so there should be a real reason to use them.

---

# 5. When to Use Microservices?

Microservices can be useful when:

* Different parts of the application need independent scaling
* Different teams need independent ownership
* Services require independent deployment
* Fault isolation is important
* Business domains are clearly separated
* The organization has sufficient CI/CD, monitoring, and operational maturity

---

# 6. Simple Example

### Monolith

```text
User
  |
  ↓
Monolithic Application
  |
  ├── Users
  ├── Orders
  ├── Products
  └── Payments
          |
          ↓
       Database
```

If the **Order** functionality needs 5 additional servers, the entire monolith may need to be scaled.

### Microservices

```text
User
  |
  ↓
API Gateway
  |
  ├── User Service ─── User DB
  |
  ├── Order Service ── Order DB
  |
  ├── Product Service ─ Product DB
  |
  └── Payment Service ─ Payment DB
```

Now the Order Service can be scaled independently.

---

# 7. Key Takeaway

```text
Monolith
    ↓
Simple
    ↓
Single deployment
    ↓
Lower operational complexity

Microservices
    ↓
Independent services
    ↓
Independent deployment/scaling
    ↓
Higher distributed-system complexity
```

> **Monolith = one application deployed as a unit.**

> **Microservices = multiple independently deployable services communicating over a network.**

---

# Interview Questions

### Q1. What is a monolithic architecture?

A single application where multiple functionalities are packaged and deployed together as one unit.

### Q2. What are microservices?

An architectural approach where an application is divided into independently deployable services organized around business capabilities.

### Q3. Why use microservices?

For independent deployment, scaling, ownership, and fault isolation when the system and organization justify the additional complexity.

### Q4. Is microservices always better than monolith?

No. Microservices introduce network, deployment, monitoring, testing, and data-consistency complexity.

### Q5. What is the biggest difference?

**Monolith:** components are deployed together.

**Microservices:** services can be deployed and scaled independently.

## Interview Memory

**Monolith → Simple but tightly coupled**

**Microservices → Independent but distributed**
