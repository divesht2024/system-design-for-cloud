
# SQL vs NoSQL Databases

## 1. What is SQL?

**SQL (Structured Query Language) databases** store data in structured tables with rows and columns.

Example:

```text
Users

ID | Name  | Email
---|-------|----------------
1  | Rahul | rahul@gmail.com
2  | Amit  | amit@gmail.com
```

SQL databases are **relational databases**, meaning data can be related across multiple tables.

Examples:

* MySQL
* PostgreSQL
* Oracle
* Microsoft SQL Server

---

# 2. What is NoSQL?

**NoSQL databases** are non-relational databases designed for flexible data models and scalable workloads.

Example of a document:

```json
{
  "id": 101,
  "name": "Rahul",
  "skills": ["AWS", "Docker", "Kubernetes"]
}
```

Another document can have different fields:

```json
{
  "id": 102,
  "name": "Amit",
  "phone": "9999999999"
}
```

Examples:

* MongoDB
* DynamoDB
* Cassandra
* Redis

> NoSQL does not mean "no SQL queries at all." It generally means **non-relational data models**.

---

# 3. SQL Data Model

SQL commonly stores data in multiple related tables.

Example:

```text
Users
  ↓
Orders
  ↓
Products
```

Example:

```text
Users
----------------
user_id
name
email

Orders
----------------
order_id
user_id
amount
```

The tables can be related using keys.

---

# 4. NoSQL Data Models

NoSQL databases can use different models.

### Document

Example:

```text
MongoDB
```

Data is stored as documents.

### Key-Value

Example:

```text
Redis
DynamoDB
```

```text
user:101 → Rahul
```

### Wide-Column

Example:

```text
Cassandra
```

### Graph

Example:

```text
Neo4j
```

For 0–2 YOE, understand the **basic idea** of these models rather than going deep into each one.

---

# 5. Main Difference

| SQL                                      | NoSQL                                       |
| ---------------------------------------- | ------------------------------------------- |
| Relational                               | Non-relational                              |
| Tables                                   | Documents / Key-Value / Wide-Column / Graph |
| Usually structured schema                | Usually flexible schema                     |
| Relationships are common                 | Often designed around access patterns       |
| SQL queries                              | Database-specific query/API models          |
| Complex joins are common                 | Often avoids joins                          |
| Strong transactions are common           | Transaction support depends on the database |
| Vertical scaling is traditionally common | Horizontal scaling is commonly emphasized   |
| MySQL, PostgreSQL                        | MongoDB, DynamoDB, Cassandra                |

---

# 6. Schema

## SQL

SQL generally uses a defined schema.

Example:

```text
Users

ID     → Integer
Name   → String
Email  → String
```

The structure is defined before storing data.

---

## NoSQL

NoSQL databases often provide a more flexible schema.

Example:

```json
{
  "name": "Rahul",
  "email": "rahul@gmail.com"
}
```

Another document may contain:

```json
{
  "name": "Amit",
  "phone": "9999999999"
}
```

This flexibility can be useful when data structure changes frequently.

> Exact schema behavior depends on the NoSQL database.

---

# 7. Relationships

SQL is very good when data has many relationships.

Example:

```text
Customer
   ↓
Orders
   ↓
Products
   ↓
Payments
```

SQL can use relationships and joins to retrieve related data.

NoSQL systems often prefer designing data around the **queries/access patterns** and may duplicate some data to avoid expensive joins.

---

# 8. Transactions

A transaction is a group of operations treated as one logical unit.

Example:

```text
Transfer ₹1000

Account A → -₹1000
Account B → +₹1000
```

You don't want only one operation to succeed.

SQL databases are widely used when strong transactional guarantees are important.

Many modern NoSQL databases also support transactions, but their transaction models and limitations differ.

---

# 9. SQL Scaling

Traditional SQL deployments often scale vertically:

```text
Small Server
2 CPU
4 GB RAM

       ↓

Bigger Server
8 CPU
32 GB RAM
```

SQL databases can also scale horizontally using techniques such as:

* Read replicas
* Partitioning
* Sharding
* Clustering

So:

> SQL does **not** mean "cannot scale horizontally."

---

# 10. NoSQL Scaling

Many NoSQL databases are designed with horizontal scaling in mind.

```text
Server 1
Server 2
Server 3
Server 4
```

More servers can be added as workload increases.

This is particularly useful for large-scale distributed workloads.

---

# 11. When to Use SQL?

Use SQL when:

* Data has strong relationships
* Transactions are important
* Complex queries are required
* Joins are useful
* Data structure is relatively well defined
* Strong consistency is important for the workload

### Examples

#### Banking

```text
Customer
   ↓
Account
   ↓
Transaction
```

#### E-commerce

```text
Customer
   ↓
Order
   ↓
Products
   ↓
Payment
```

SQL is often a good choice for these workloads.

---

# 12. When to Use NoSQL?

Use NoSQL when:

* Data structure needs flexibility
* Very large scale is required
* High request volume is expected
* Data naturally fits a document/key-value/wide-column model
* Access patterns are well understood
* Horizontal scaling is important

### Examples

* User sessions
* Product catalogs with varying attributes
* Large-scale event data
* Real-time applications
* High-volume key-value access

---

# 13. Simple Decision Rule

```text
Do I have strong relationships,
complex queries and transactions?
             ↓
            YES
             ↓
            SQL
```

```text
Do I need flexible data models,
large-scale distributed access,
and predictable high-volume access patterns?
             ↓
            YES
             ↓
           NoSQL
```

### Memory

```text
SQL   → Relationships + Transactions + Complex Queries

NoSQL → Flexibility + Scale + Specific Access Patterns
```

---

# 14. SQL Example — E-Commerce

Suppose we have:

```text
Users
Orders
Products
Payments
```

Relationships:

```text
User
 ↓
Order
 ↓
Product
 ↓
Payment
```

SQL is a natural choice because the application has strong relationships and transactional operations.

---

# 15. NoSQL Example — Product Catalog

Different products can have different attributes.

### Mobile

```json
{
  "name": "Phone",
  "brand": "ABC",
  "ram": "8GB",
  "storage": "128GB"
}
```

### Shoes

```json
{
  "name": "Running Shoe",
  "brand": "XYZ",
  "size": 9,
  "material": "Mesh"
}
```

A flexible document model can be convenient for this type of data.

---

# 16. SQL + NoSQL Together

Real systems don't necessarily choose only one database.

Example:

```text
                  Application
                 /           \
                ↓             ↓
             SQL DB        NoSQL DB
                ↓             ↓
          Transactions    Sessions/
          Orders/Users     Cache/Data
```

An application can use different databases for different workloads.

Example:

```text
PostgreSQL
→ Orders and payments

Redis
→ Sessions / caching

DynamoDB
→ High-scale key-value workload
```

The choice should be based on the requirements.

---

# 17. SQL vs NoSQL vs Cache

Don't confuse a database with a cache.

```text
SQL / NoSQL
     ↓
Persistent data storage
```

```text
Redis
     ↓
Often used as a cache
```

Redis can also be used for other data structures/use cases, so it should not simply be described as "only a cache."

---

# 18. AWS Examples

### SQL

AWS managed relational database:

```text
Amazon RDS
```

Examples of engines include:

* MySQL
* PostgreSQL
* MariaDB
* Oracle
* SQL Server

AWS also provides **Amazon Aurora**, a managed relational database engine compatible with MySQL and PostgreSQL.

---

### NoSQL

```text
Amazon DynamoDB
```

DynamoDB is a managed NoSQL database designed for highly scalable applications.

It primarily uses:

* Partition keys
* Sort keys
* Items
* Attributes

Basic example:

```text
Partition Key → user_id
Sort Key       → order_id
```

---

# 19. SQL vs NoSQL in AWS Architecture

### SQL Architecture

```text
User
 ↓
ALB
 ↓
EC2 / ECS
 ↓
RDS / Aurora
```

### NoSQL Architecture

```text
User
 ↓
API Gateway / ALB
 ↓
Application
 ↓
DynamoDB
```

---

# 20. Interview Questions

### Q1. What is SQL?

> SQL databases are relational databases that store structured data in tables and support relationships, queries, and transactions.

### Q2. What is NoSQL?

> NoSQL databases are non-relational databases that use models such as document, key-value, wide-column, or graph and are often designed for flexible schemas and scalable distributed workloads.

### Q3. SQL vs NoSQL?

> SQL is generally preferred for relational data, complex queries, and transactional workloads, while NoSQL can be a good choice for flexible data models, high-scale workloads, and applications with well-defined access patterns.

### Q4. When would you choose SQL?

> I would choose SQL when the application has strong relationships, transactions, complex queries, and structured data.

### Q5. When would you choose NoSQL?

> I would choose NoSQL when flexible data models, high-scale distributed access, or a specific key-value/document access pattern is more important.

### Q6. Can SQL scale horizontally?

> Yes. SQL databases can use techniques such as read replicas, partitioning, sharding, and clustering. SQL does not inherently mean only vertical scaling.

### Q7. Is NoSQL always faster than SQL?

> No. Performance depends on the workload, data model, queries, indexing, hardware, and database design.

### Q8. Is NoSQL always better for large-scale applications?

> No. The database should be selected based on application requirements, consistency needs, relationships, query patterns, and scaling requirements.

### Q9. Can an application use both SQL and NoSQL?

> Yes. Different databases can be used for different workloads within the same application.

### Q10. RDS vs DynamoDB?

> RDS is a managed relational database service suitable for structured relational workloads, while DynamoDB is a managed NoSQL database designed for highly scalable key-value and document workloads.

---

# 21. 30-Second Interview Answer

> **"SQL databases are relational databases that store structured data in tables. They are a good choice when we have strong relationships, complex queries, and transactional requirements. NoSQL databases use models such as document or key-value and are useful when we need flexible data models, large-scale distributed access, or specific high-volume access patterns. I would choose between them based on the application's data model, query patterns, consistency requirements, transactions, and scaling needs."**

---

# 🔥 Final Memory

```text
                Database Choice
                      ↓
          ┌───────────┴───────────┐
          ↓                       ↓
         SQL                    NoSQL
          ↓                       ↓
   Relationships              Flexibility
   Transactions                  Scale
   Complex Queries         Access Patterns
   Structured Data        Distributed Workload
```

### Golden Rule

> **Don't choose SQL or NoSQL simply because one is "better." Choose the database that matches the application's data model and workload.**
