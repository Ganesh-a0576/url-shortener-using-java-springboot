This project implements a URL shortening service similar to Bitly.
It converts long URLs into short unique links and redirects users efficiently.

The system demonstrates backend engineering concepts including:

• REST API development
• Redis caching
• Database persistence
• Base62 encoding for short codes
• Scalable service architecture

This section describes how requests flow through the system.

Where to place architecture diagram:

Save the diagram in the repository:

```
Architecture.png  

```

Then reference it here:

Request Flow

1. Client sends a request to shorten a URL
2. Spring Boot API receives the request
3. Service generates a short code using Base62 encoding
4. URL mapping stored in PostgreSQL
5. Redis cache stores frequently accessed URLs
6. Redirect requests served directly from Redis for faster performance

Backend 
Java

Framework 
Spring Boot

Cache Layer 
Redis

Database 
PostgreSQL

Build Tool 
Maven

Containerization 
Docker

```
url-shortener

src/main/java/com/urlshortener

controller
 └── URLController.java

service
 └── URLConverterService.java

repository
 └── URLRepository.java

common
 ├── IDConverter.java
 └── URLValidator.java

URLShortenerApplication.java
```

IDConverter.java 
A Singleton class responsible for:

1. Generating numeric IDs
2. Converting numeric IDs into Base62 short codes
3. Decoding short codes back to original IDs

URLValidator.java 
A utility class responsible for validating URL format before processing.

URLController.java 
A Spring Boot controller responsible for:

1. Accepting URL shortening requests
2. Redirecting shortened URLs to the original URL

URLRepository.java 
Handles read and write operations to Redis and PostgreSQL.

URLConverterService.java 
Implements the core business logic including:

1. URL shortening process
2. URL retrieval and redirect logic

URLShortenerApplication.java 
Entry point for the Spring Boot application.

Create Short URL

POST /shortener

Request Body:

```
{
"url": "https://example.com/very-long-url"
}
```

Response:

```
{
"shortUrl": "http://localhost:8080/aB12X"
}
```

Redirect URL

GET /{shortCode}

Example:

```
http://localhost:8080/aB12X
```

Response:

HTTP 302 Redirect to original URL.

This project can be executed using Docker containers.

Step 1 — Clone repository

```
git clone https://github.com/<your-username>/url-shortener
cd url-shortener
```

Step 2 — Start services

```
docker-compose up --build
```

This command will start:

• Spring Boot Application
• Redis Server
• PostgreSQL Database

1. Start Redis Server

```
redis-server
```

2. Build the project

```
mvn clean install
```

3. Run the application

```
mvn spring-boot:run
```

The server will run at:

```
http://localhost:8080
```

Send POST request:

```
POST http://localhost:8080/shortener
```

Body:

```
{
"url":"https://google.com"
}
```

Redis is used to cache frequently accessed URLs.

Flow:

1. Check Redis cache for short code
2. If found → redirect immediately
3. If not found → fetch from PostgreSQL
4. Store result in Redis for future requests

This system can scale by:

• Adding load balancers
• Running multiple Spring Boot instances
• Redis clustering
• PostgreSQL replication

Possible enhancements include:

• Custom alias URLs
• Link expiration
• Analytics dashboard for clicks
• API rate limiting
• User authentication
