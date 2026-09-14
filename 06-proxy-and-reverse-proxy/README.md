# 06. Proxy and Reverse Proxy

## 1. What is a Proxy?

A **Proxy Server** is an intermediate server between a **client** and a destination server.

```text
Client → Proxy → Internet / Server
```

The proxy sends requests on behalf of the client.

### Common Uses

* Hide client IP
* Control internet access
* Filter requests
* Monitor traffic
* Cache responses
* Apply security policies

---

# 2. Forward Proxy

A **Forward Proxy** works on behalf of the **client**.

```text
Client
   |
   ↓
Forward Proxy
   |
   ↓
Internet / Server
```

The destination server sees the **proxy** as the source rather than directly seeing the client.

### Example

A company may configure:

```text
Employee → Corporate Proxy → Internet
```

The company can use the proxy to:

* Block certain websites
* Monitor traffic
* Apply access policies
* Control outbound connections

### Memory

> **Forward Proxy = Client side**

---

# 3. Reverse Proxy

A **Reverse Proxy** is an intermediate server between clients and backend servers.

```text
Client
   |
   ↓
Reverse Proxy
   |
   ↓
Backend Server
```

The client communicates with the reverse proxy rather than directly with the backend.

### Common Uses

* Hide backend servers
* Request routing
* Load balancing
* SSL/TLS termination
* Caching
* Compression
* Security filtering
* Rate limiting

---

# 4. Reverse Proxy Example

Suppose an application has multiple backend servers:

```text
                  ┌──→ Server 1
Client → Reverse ─┼──→ Server 2
         Proxy    └──→ Server 3
```

The reverse proxy receives the client request and forwards it to an appropriate backend.

For example:

```text
/api/users  → User Service
/api/orders → Order Service
```

---

# 5. Reverse Proxy vs Load Balancer

These concepts are related but not identical.

### Reverse Proxy

Focuses on:

> **"Where/how should I forward this request?"**

### Load Balancer

Focuses on:

> **"Which healthy backend should handle this request?"**

A reverse proxy **can also perform load balancing**.

Example:

```text
Client
   |
   ↓
Nginx Reverse Proxy
   |
   ├──→ Server 1
   ├──→ Server 2
   └──→ Server 3
```

Here, Nginx is acting as both:

* Reverse Proxy
* Load Balancer

---

# 6. Forward Proxy vs Reverse Proxy

| Feature           | Forward Proxy            | Reverse Proxy          |
| ----------------- | ------------------------ | ---------------------- |
| Acts on behalf of | Client                   | Server                 |
| Location          | Client side              | Server side            |
| Hides             | Client                   | Backend                |
| Main purpose      | Control outbound traffic | Manage inbound traffic |
| Example           | Corporate proxy          | Nginx                  |

### Easy Memory

> **Forward Proxy → protects/represents clients**
> **Reverse Proxy → protects/manages servers**

---

# 7. Common Reverse Proxy Software

Common technologies include:

* Nginx
* HAProxy
* Apache HTTP Server

Cloud services can also provide reverse-proxy functionality.

---

# 8. Reverse Proxy + Load Balancer

A typical architecture can look like:

```text
Users
  |
  ↓
Reverse Proxy / Load Balancer
  |
  ├──→ App Server 1
  ├──→ App Server 2
  └──→ App Server 3
```

The reverse proxy can:

1. Receive requests
2. Terminate TLS
3. Apply security rules
4. Route requests
5. Distribute traffic
6. Forward requests to backend servers

---

# 9. Why Use a Reverse Proxy?

Without a reverse proxy:

```text
Client → Backend Server
```

With a reverse proxy:

```text
Client → Reverse Proxy → Backend Server
```

This provides a central point for:

* Security
* Routing
* TLS termination
* Load balancing
* Caching
* Monitoring

It also prevents clients from needing to know the internal backend server details.

---

# 10. Real-World Example

Consider an e-commerce application:

```text
                    ┌──→ User Service
                    |
Client → Nginx ─────┼──→ Order Service
                    |
                    └──→ Payment Service
```

Nginx can act as a reverse proxy and route requests based on the URL.

```text
/app/users   → User Service
/app/orders  → Order Service
/app/payment → Payment Service
```

---

# 11. Proxy vs Reverse Proxy vs Load Balancer

| Component         | Main Job                                   |
| ----------------- | ------------------------------------------ |
| **Forward Proxy** | Represents clients                         |
| **Reverse Proxy** | Represents/manages backend servers         |
| **Load Balancer** | Distributes traffic across backend targets |

### Memory

```text
Forward Proxy
    ↓
Client → Proxy → Internet

Reverse Proxy
    ↓
Client → Proxy → Server

Load Balancer
    ↓
Client → LB → Server 1 / Server 2 / Server 3
```

---

# 12. Interview Questions

### Q1. What is a proxy?

A proxy is an intermediate server that forwards requests between a client and a destination server.

### Q2. What is a forward proxy?

A forward proxy acts on behalf of the client and is commonly used for outbound traffic control, filtering, and monitoring.

### Q3. What is a reverse proxy?

A reverse proxy acts on behalf of backend servers and handles incoming client requests before forwarding them to backend systems.

### Q4. Why use a reverse proxy?

For routing, load balancing, TLS termination, security, caching, and hiding backend infrastructure.

### Q5. What is the difference between forward and reverse proxy?

**Forward proxy represents the client; reverse proxy represents the server.**

### Q6. Can a reverse proxy perform load balancing?

Yes. Technologies such as Nginx can act as both a reverse proxy and a load balancer.

### Q7. Is a reverse proxy the same as a load balancer?

No.

A reverse proxy is a broader server-side intermediary. A load balancer specifically distributes traffic across backend targets.

---

# Key Takeaway

```text
Forward Proxy
Client → Proxy → Internet

Reverse Proxy
Client → Reverse Proxy → Backend

Load Balancer
Client → Load Balancer → Backend 1/2/3
```

### 🔥 Interview Memory

> **Forward Proxy = Client side**
> **Reverse Proxy = Server side**
> **Load Balancer = Distributes traffic**
