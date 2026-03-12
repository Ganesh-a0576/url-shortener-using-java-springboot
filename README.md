#  URL Shortener

> A high-performance URL shortening service built with Java, Spring Boot, Redis, and PostgreSQL — inspired by Bitly.

---

##  Overview

This project implements a production-grade **URL shortening service** that converts long URLs into compact, shareable links and redirects users with minimal latency.

It demonstrates core backend engineering principles across the full stack:

| Concept | Implementation |
|---|---|
| REST API Design | Spring Boot Controllers |
| Caching Strategy | Redis (in-memory) |
| Data Persistence | PostgreSQL |
| ID Generation | Base62 Encoding |
| Containerization | Docker Compose |
| Scalable Architecture | Stateless services + DB replication |

---

##  Architecture

<img width="2156" height="725" alt="null" src="https://github.com/user-attachments/assets/7c6df63f-c572-4962-9499-e661c158394a" />


### Request Flow

1. **Client** sends a `POST /shortener` request with a long URL
2. **Spring Boot API** receives and validates the request
3. **URLConverterService** generates a unique short code via Base62 encoding
4. **URL mapping** is persisted in PostgreSQL
5. **Redis** caches the mapping for fast future lookups
6. **Redirect requests** are served directly from Redis when cached, falling back to PostgreSQL on cache miss

> 📷 Place your architecture diagram at `Architecture.png` in the project root and reference it here.

---

##  Tech Stack

| Layer | Technology |
|---|---|
| **Language** | Java |
| **Framework** | Spring Boot |
| **Cache** | Redis |
| **Database** | PostgreSQL |
| **Build Tool** | Maven |
| **Containers** | Docker + Docker Compose |

---

##  Project Structure

```
url-shortener/
└── src/main/java/com/urlshortener/
    ├── controller/
    │   └── URLController.java          # REST API endpoints
    ├── service/
    │   └── URLConverterService.java    # Core business logic
    ├── repository/
    │   └── URLRepository.java          # Redis + PostgreSQL I/O
    ├── common/
    │   ├── IDConverter.java            # Base62 encoding (Singleton)
    │   └── URLValidator.java           # URL format validation
    └── URLShortenerApplication.java    # Application entry point
```

### Component Descriptions

**`IDConverter.java`** — Singleton responsible for:
- Generating numeric IDs
- Converting numeric IDs into Base62 short codes (e.g. `aB12X`)
- Decoding short codes back to their original numeric IDs

**`URLValidator.java`** — Utility class that validates URL format and structure before processing.

**`URLController.java`** — Spring Boot REST controller that:
- Accepts URL shortening requests (`POST /shortener`)
- Handles redirect logic for short codes (`GET /{shortCode}`)

**`URLRepository.java`** — Data access layer handling read/write operations to both Redis and PostgreSQL.

**`URLConverterService.java`** — Core service implementing:
- URL shortening pipeline
- URL retrieval and redirect resolution

**`URLShortenerApplication.java`** — Spring Boot application entry point.

---

##  API Reference

### Shorten a URL

```http
POST /shortener
Content-Type: application/json
```

**Request Body:**
```json
{
  "url": "https://example.com/very-long-url"
}
```

**Response:**
```json
{
  "shortUrl": "http://localhost:8080/aB12X"
}
```

---

### Redirect via Short Code

```http
GET /{shortCode}
```

**Example:**
```
GET http://localhost:8080/aB12X
```

**Response:** `HTTP 302 Redirect` → original URL

---

##  Getting Started

### Option A — Docker (Recommended)

**Step 1 — Clone the repository**
```bash
git clone https://github.com/<your-username>/url-shortener
cd url-shortener
```

**Step 2 — Start all services**
```bash
docker-compose up --build
```

This spins up:
-  Spring Boot Application
-  Redis Server
-  PostgreSQL Database

---

### Option B — Run Locally

**1. Start Redis**
```bash
redis-server
```

**2. Build the project**
```bash
mvn clean install
```

**3. Run the application**
```bash
mvn spring-boot:run
```

The server starts at → `http://localhost:8080`

---

###  Quick Test

```bash
curl -X POST http://localhost:8080/shortener \
  -H "Content-Type: application/json" \
  -d '{"url": "https://google.com"}'
```

---

##  Caching Strategy

Redis is used to cache frequently accessed URL mappings for near-instant redirects.

**Cache Resolution Flow:**

```
Incoming short code
       │
       ▼
  Redis cache hit? ──Yes──▶ Redirect immediately
       │
      No
       │
       ▼
 Query PostgreSQL
       │
       ▼
 Store in Redis ──────────▶ Redirect to original URL
```

---

##  Scalability

This architecture is designed to scale horizontally:

- **Load Balancers** — Distribute traffic across multiple Spring Boot instances
- **Stateless API Layer** — Any instance can handle any request
- **Redis Clustering** — Distributed cache across multiple nodes
- **PostgreSQL Replication** — Read replicas reduce DB bottlenecks

---

##  Planned Enhancements

- [ ] Custom alias URLs (e.g. `/my-link`)
- [ ] Link expiration with TTL support
- [ ] Analytics dashboard (click counts, geolocation)
- [ ] API rate limiting per client
- [ ] User authentication and link ownership

---

##  License

Apache License 2.0 © Bathula Ganesh
