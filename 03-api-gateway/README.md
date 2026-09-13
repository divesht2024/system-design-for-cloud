# API Gateway

## 1. What is an API Gateway?

An **API Gateway** is a single entry point between clients and backend services.

It receives client requests, determines where they should go, and forwards them to the appropriate backend service.

```text
                 ┌─────────────────┐
                 │     Client      │
                 └────────┬────────┘
                          │
                          ▼
                 ┌─────────────────┐
                 │   API Gateway   │
                 └───────┬─────────┘
                         │
             ┌───────────┼───────────┐
             ▼           ▼           ▼
        User Service  Order Service  Payment Service
```

Instead of clients directly communicating with multiple backend services, they communicate through the API Gateway.

---

## 2. Why do we need an API Gateway?

Without an API Gateway:

```text
Client → User Service
Client → Order Service
Client → Payment Service
Client → Notification Service
```

The client needs to know about multiple services.

With an API Gateway:

```text
                 ┌──────────────┐
Client ─────────►│ API Gateway  │
                 └──────┬───────┘
                        │
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
       User          Order         Payment
      Service        Service        Service
```

The client only needs to know the API Gateway.

### Main benefits

* Single entry point
* Request routing
* Authentication/authorization
* Rate limiting/throttling
* Request/response transformation
* Logging and monitoring
* API version management
* Hides internal service details from clients

---

## 3. How does API Gateway work?

Suppose a client sends:

```http
GET /users/123
```

The request flow can be:

```text
Client
   │
   │ GET /users/123
   ▼
API Gateway
   │
   │ Route request
   ▼
User Service
   │
   ▼
Database
```

The API Gateway:

1. Receives the request.
2. Checks the request.
3. Applies security policies if configured.
4. Determines the correct route.
5. Sends the request to the backend.
6. Receives the response.
7. Returns the response to the client.

API Gateway routes are commonly based on an HTTP method and resource path, such as `GET /users` or `POST /orders`.

---

## 4. API Gateway Responsibilities

### 4.1 Request Routing

Routes requests to the correct backend service.

```text
GET /users/*      → User Service
GET /products/*   → Product Service
POST /orders      → Order Service
POST /payments    → Payment Service
```

---

### 4.2 Authentication & Authorization

The Gateway can participate in authentication and authorization.

Example:

```text
Client
  │
  │ Request + Token
  ▼
API Gateway
  │
  │ Validate authorization
  ▼
Backend Service
```

Common mechanisms include:

* JWT
* OAuth
* IAM
* Lambda authorizers

**Important:** API keys are generally intended for identifying/controlling usage, not as the primary authentication/authorization mechanism. AWS recommends stronger authorization mechanisms such as IAM, Lambda authorizers, or Cognito where appropriate.

---

### 4.3 Rate Limiting / Throttling

API Gateway can limit how many requests clients can send.

Example:

```text
Client
  │
  │ 10,000 requests/sec
  ▼
API Gateway
  │
  │ Allowed requests
  ▼
Backend
```

If the configured limit is exceeded, requests can be throttled and the client may receive:

```text
HTTP 429 Too Many Requests
```

AWS API Gateway uses token-bucket-based throttling.

---

### 4.4 Logging and Monitoring

API Gateway can provide visibility into API traffic.

Useful information includes:

* Request count
* Errors
* Latency
* HTTP status codes
* Throttled requests

This helps troubleshoot API-related problems.

---

### 4.5 Request Transformation

The Gateway can modify or transform requests before sending them to backend services.

For example:

```text
Client Request
      │
      ▼
API Gateway
      │
      │ Transform
      ▼
Backend Service
```

---

## 5. API Gateway vs Load Balancer

This is an important interview question.

| API Gateway                      | Load Balancer                                 |
| -------------------------------- | --------------------------------------------- |
| API entry point                  | Traffic distribution layer                    |
| Routes requests to services/APIs | Distributes traffic across backend instances  |
| Can handle authentication        | Mainly handles traffic distribution           |
| Can handle throttling            | Can perform health checks                     |
| Can transform requests           | Can route based on configured rules           |
| Works at API/service level       | Commonly distributes traffic across instances |

### Easy memory trick

> **API Gateway = Which service should receive the request?**

> **Load Balancer = Which instance should handle the request?**

Example:

```text
Client
  │
  ▼
API Gateway
  │
  │ /orders
  ▼
Order Load Balancer
  │
  ├── Order Server 1
  ├── Order Server 2
  └── Order Server 3
```

The API Gateway can determine that `/orders` belongs to the Order Service.

The Load Balancer can then distribute the request among healthy Order Service instances.

---

## 6. API Gateway vs Reverse Proxy

A **Reverse Proxy** forwards client requests to backend servers.

An **API Gateway** is more API-management-oriented and can provide additional capabilities such as:

* Authentication
* Authorization
* Rate limiting
* API routing
* API versioning
* Request transformation
* Monitoring

Simple view:

```text
Reverse Proxy
     │
     └── Forward traffic

API Gateway
     │
     ├── Route
     ├── Authenticate
     ├── Authorize
     ├── Rate Limit
     ├── Transform
     └── Monitor
```

An API Gateway can therefore act as a specialized reverse-proxy layer for APIs, but the terms are not interchangeable in every architecture.

---

## 7. API Gateway in Microservices

API Gateway is commonly used with microservices.

```text
                         ┌───────────────┐
                         │    Client     │
                         └───────┬───────┘
                                 │
                                 ▼
                         ┌───────────────┐
                         │  API Gateway  │
                         └───────┬───────┘
                                 │
                ┌────────────────┼────────────────┐
                ▼                ▼                ▼
          User Service     Order Service    Payment Service
                │                │                │
                ▼                ▼                ▼
             User DB          Order DB        Payment DB
```

This provides a clean external interface while allowing backend services to change independently.

---

## 8. AWS API Gateway

**Amazon API Gateway** is a managed AWS service for creating, publishing, securing, monitoring, and managing APIs.

It can integrate with:

* AWS Lambda
* EC2 applications
* ECS applications
* HTTP backends
* Other AWS services

AWS supports:

* REST APIs
* HTTP APIs
* WebSocket APIs

Example:

```text
                  Internet
                     │
                     ▼
             ┌───────────────┐
             │ API Gateway   │
             └───────┬───────┘
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
       Lambda                  ECS
          │                     │
          ▼                     ▼
       DynamoDB                RDS
```

---

## 9. API Gateway Routing Example

Suppose we have:

```text
GET  /users
GET  /users/{id}
POST /orders
GET  /products
```

The Gateway can route them as:

```text
GET /users          → User Service
GET /users/101      → User Service
POST /orders        → Order Service
GET /products       → Product Service
```

The route determines which backend integration receives the request.

---

## 10. API Gateway + Load Balancer

A common architecture can be:

```text
                    Client
                       │
                       ▼
                ┌─────────────┐
                │ API Gateway │
                └──────┬──────┘
                       │
              ┌────────┴────────┐
              ▼                 ▼
          User Service     Order Service
              │                 │
              ▼                 ▼
             ALB               ALB
              │                 │
        ┌─────┴─────┐     ┌─────┴─────┐
        ▼           ▼     ▼           ▼
       EC2         EC2   EC2         EC2
```

### Important

API Gateway and Load Balancer are **not always required together**.

The architecture depends on the system requirements.

---

## 11. Advantages

### API Gateway provides:

* Centralized API access
* Service routing
* Authentication/authorization integration
* Rate limiting
* Monitoring
* Request transformation
* API version management
* Reduced client-to-service complexity

---

## 12. Disadvantages

API Gateway also introduces additional complexity.

### Possible disadvantages:

* Additional network hop
* Added latency
* Gateway configuration complexity
* Gateway failure can affect many services if not designed properly
* Additional cost
* Requires monitoring and maintenance

---

## 13. Interview Questions

### Q1. What is an API Gateway?

**Answer:**

> An API Gateway is a single entry point for clients to access backend services. It routes requests to the appropriate service and can also provide features such as authentication, authorization, rate limiting, request transformation, and monitoring.

---

### Q2. Why do we need an API Gateway?

**Answer:**

> It provides a single entry point for clients and hides internal service details. It also centralizes common API concerns such as authentication, routing, throttling, and monitoring.

---

### Q3. API Gateway vs Load Balancer?

**Answer:**

> API Gateway decides which API or backend service should handle a request, while a Load Balancer distributes traffic across multiple instances of a backend service.

---

### Q4. Can API Gateway and Load Balancer be used together?

**Answer:**

> Yes. For example, API Gateway can route `/orders` to an Order Service, and a Load Balancer can distribute that traffic across multiple Order Service instances.

---

### Q5. What happens if a client sends too many requests?

**Answer:**

> The API Gateway can apply throttling or rate limiting. If the configured limit is exceeded, requests may be rejected with HTTP 429 Too Many Requests.

---

### Q6. Is API Gateway always required in microservices?

**Answer:**

> No. It depends on the architecture and requirements. An API Gateway is useful when we need centralized routing, authentication, rate limiting, or API management.

---

## 14. Key Takeaway

Remember:

```text
API Gateway
     │
     ├── Single Entry Point
     ├── Routing
     ├── Authentication
     ├── Authorization
     ├── Rate Limiting
     ├── Request Transformation
     └── Monitoring
```

### One-line interview memory

> **API Gateway = Single entry point that manages and routes client API requests to backend services.**

### Memory Trick

```text
CLIENT
  ↓
API GATEWAY
  ↓
WHICH SERVICE?
  ↓
LOAD BALANCER
  ↓
WHICH INSTANCE?
```

---

## 15. Interview Focus

For a **0–2 YOE Cloud/DevOps interview**, focus on:

* What is API Gateway?
* Why is it needed?
* How does request routing work?
* API Gateway vs Load Balancer
* API Gateway vs Reverse Proxy
* Authentication/authorization
* Rate limiting/throttling
* API Gateway with microservices
* API Gateway + Load Balancer architecture
* Basic AWS API Gateway understanding

Do **not** go deep into advanced API Gateway internals at this stage.
