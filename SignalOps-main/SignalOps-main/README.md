# 🚨 Mission-Critical Incident Management System (IMS)

A production-grade Incident Management System built to monitor distributed systems (APIs, MCP hosts, caches, databases) and manage the complete incident lifecycle — from signal ingestion to root cause analysis.

Designed to handle **10,000+ signals per second** with efficient asynchronous processing and strong backpressure mechanisms.

---

## 🏗️ Architecture Overview

```
+----------------+      +-------------------+      +--------------------------+
| Client/Sensors | ---> | Sliding Window    | ---> | Bounded In-Memory Queue  |
|                | POST | Rate Limiter      |      | (max 50,000 events)      |
+----------------+      +-------------------+      +--------------------------+
                                                           |
                                                           | Processed by
                                                           | Async Workers
                                                           v
+------------------+     +------------------+     +--------------------------+
| React Dashboard  | <---| WebSocket Layer  | <---| PostgreSQL (Supabase)    |
+------------------+     +------------------+     +--------------------------+
```

---

## ⚡ Performance & Reliability

Built with real-world system stress and failure scenarios in mind.

### Backpressure Handling

* Uses a bounded `asyncio.Queue` (limit: 50,000 events)
* On overload:

  * Returns `503 Service Unavailable`
  * Includes `retry_after_ms`
* Prevents crashes and protects downstream services

### Rate Limiting

* Sliding window algorithm
* Limits each IP to **500 requests/second**
* Filters abusive traffic early

### Async Processing & DB Optimization

* Background workers batch-write events
* Uses `asyncpg` connection pooling
* Reduces connection overhead and improves throughput

### In-Memory Caching

* Dashboard data served directly from memory
* Minimizes database load for frequent reads

---

## 🔐 Security

* **SSL Enforcement:**
  PostgreSQL connections use `sslmode=require`

* **CORS Restrictions:**
  Only trusted frontend origins can access APIs and WebSockets

* **Environment-Based Secrets:**
  No hardcoded credentials — all configs via environment variables

---

## 🧰 Tech Stack

* **Backend:** Python, FastAPI, asyncpg
* **Database:** Supabase (PostgreSQL)
* **Frontend:** React, Vite, Tailwind CSS, TanStack Query
* **Deployment:** Docker, Docker Compose

---

## 🚀 Getting Started

### 1. Set Environment Variable

```bash
export POSTGRES_DSN="postgresql://postgres.[project-id]:[password]@aws-0.pooler.supabase.com:6543/postgres?sslmode=require"
```

### 2. Run the Application

```bash
docker-compose up --build -d
```

### 3. Access the System

* Frontend → http://localhost:3000
* API Docs → http://localhost:8000/docs

---

## 🧪 Simulate High Load

Generate high traffic using the seed script:

```bash
python3 backend/scripts/seed.py --url http://localhost:8000
```

Watch real-time incident updates on the dashboard via WebSockets.

---

## 💡 Key Highlights

* Handles **10k+ signals per second**
* Implements **graceful backpressure instead of failure**
* Real-time updates using **WebSockets**
* Optimized using **async architecture + connection pooling**
* Designed with **SRE principles (scalability, resilience, fault tolerance)**

---

## 📌 Future Improvements

* Distributed queue (Kafka / Redis Streams)
* Alerting integrations (Slack, PagerDuty)
* Horizontal scaling with load balancers
* Advanced incident analytics & RCA visualization

---

## 📜 License

This project is open-source and available under the MIT License.

---
