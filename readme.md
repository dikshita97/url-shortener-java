# URL Shortener

A **production‑grade, horizontally scalable URL Shortener** built with **Java & Spring Boot**, designed using real system‑design principles rather than tutorial shortcuts.

This project focuses on **scalability, performance, resilience, and clean architecture**, and demonstrates how real backend systems are built.

---

## 🚀 Features

- Short URL generation
- Fast redirection (read‑heavy optimized)
- Distributed‑safe ID generation
- Redis‑based caching
- Redis‑based rate limiting
- True horizontal database scaling using MySQL sharding
- Graceful degradation when Redis is unavailable

---

## 🏗 High‑Level Architecture

```
Client
  ↓
Load Balancer
  ↓
Stateless Spring Boot Application
  ↓
Redis (Cache | Rate Limiter | ID Generator)
  ↓
Shard Router
  ↓
MySQL Shards (Multiple Instances)
```

---

## 🧠 Key Design Decisions

### 1️⃣ Stateless Application Layer
- No session or in‑memory state
- Horizontally scalable application servers

### 2️⃣ Redis as an Infrastructure Component
Redis is used for:
- **Cache‑aside pattern** for redirects
- **Atomic ID generation** using `INCR`
- **Rate limiting** on write paths

Redis is treated as an **optimization layer**, not a source of truth.

---

## 🧩 Distributed ID Generation

**Problem:**
- In‑memory counters break in distributed systems
- UUIDs are too long

**Solution:**
```
Redis INCR → Numeric ID → Base62 Encoding → Short Code
```

**Benefits:**
- Globally unique IDs
- Works across multiple app instances
- Restart safe

---

## ⚡ Caching Strategy (Read Optimization)

- Cache‑aside pattern
- Key: `shortCode → longUrl`
- TTL‑based eviction

**Redirect Flow:**
```
GET /{shortCode}
 → Redis (HIT) → Redirect
 → Redis (MISS) → MySQL → Redis → Redirect
```

---

## 🛡 Rate Limiting

- Applied on `POST /shorten`
- Redis‑backed sliding window
- IP‑based throttling
- Returns HTTP `429 Too Many Requests`

**Fail‑open strategy:**
- If Redis is unavailable, requests are allowed to preserve availability

---

## 🗄 Database Horizontal Scaling (Core Highlight)

### Why Sharding?
- Single MySQL instance is a bottleneck
- URL Shortener is a perfect candidate for sharding

### Shard Strategy
- **Shard Key:** `shortCode`
- **Shard Function:**
```
shardId = abs(hash(shortCode)) % N
```

### Properties
- Deterministic routing
- No cross‑shard joins
- Single‑row lookups

Each shard is an **independent MySQL instance** with identical schema.

---

## 🧱 Persistence Layer

- Spring Data JPA intentionally avoided
- Uses **JdbcTemplate** for full control
- Shard routing handled at application layer

This mirrors how sharded systems are implemented in real production environments.

---

## 🧯 Resilience & Failure Handling

- Redis failures do not crash the application
- Graceful fallback to MySQL
- Short timeouts for external dependencies

**Design Principle:**
> Availability over optimization

---

## 🧪 Testing & Validation

- APIs tested using Swagger & curl
- Shard routing verified by checking multiple MySQL instances
- Redis failures tested by stopping Redis container

---

## 🛠 Tech Stack

- Java 
- Spring Boot
- Spring Web
- Spring JDBC
- Redis
- MySQL
- Docker (for Redis / MySQL instances)

---

## 📌 Project Status

**Completed (v1):**
- Core functionality
- Scalability
- Safety
- Resilience
- Horizontal DB scaling

The project is **feature‑complete and interview‑ready**.

---

## 🎯 Summary

> Built a URL Shortener with a stateless Spring Boot backend, Redis for caching, rate limiting, and distributed ID generation, and horizontally scaled the database using hash‑based sharding across multiple MySQL instances. The system gracefully degrades when Redis is unavailable to ensure high availability.

---
