<div align="center">

# Fundamentals of Backend Architecture

**Rajon Talukdar**

_June 17, 2026_

</div>

---

## 1. Stateful Servers

A stateful server **remembers information** about a user **between requests**. The server **stores session data in RAM**.

```mermaid
flowchart TD
    A[User logs in] --> B[Server stores session in memory]
    B --> C[User requests profile]
    C --> D[Server remembers user from session]
```

| ✅ Advantages | ❌ Disadvantages |
| --- | --- |
| Easy to implement | Difficult to scale |
| Fits traditional web apps | Session lost if server crashes |
| | Load balancer must pin user to the same server |

---

## 2. Stateless Servers

A stateless server **stores no information** about a user **between requests**. Each request carries everything needed to be processed.

```mermaid
flowchart TD
    A[User logs in] --> B[Server generates JWT]
    B --> C[Client stores JWT]
    C --> D[Client sends JWT on every request]
    D --> E[Server validates token, no stored session]
```

| ✅ Advantages | ❌ Disadvantages |
| --- | --- |
| Easy horizontal scaling | Token management required |
| Great for mobile apps | |
| Cloud-friendly | |

---

## 3. Load Balancing

When one **server cannot handle all traffic**, **requests** are **distributed** across **multiple servers**.

```mermaid
flowchart TD
    U[Users] --> LB[Load Balancer]
    LB --> S1[Server 1]
    LB --> S2[Server 2]
    LB --> S3[Server 3]
```

**Responsibilities**

- Distribute traffic
- Health checks
- Failover
- SSL termination

**Popular load balancers:** Nginx · HAProxy · Traefik · Envoy

---

## 4. Nginx Example

Nginx is commonly used as a **reverse proxy**, **load balancer**, and **static file server**.

```mermaid
flowchart TD
    C[Client] --> N[Nginx]
    N --> A[Node.js App]
```

**Example config:**

```nginx
upstream backend {
    server 10.0.0.1:3000;
    server 10.0.0.2:3000;
    server 10.0.0.3:3000;
}

server {
    listen 80;
    location / {
        proxy_pass http://backend;
    }
}
```

---

## 5. Different Types of Scaling

### Vertical Scaling — increase the power of one server

```mermaid
flowchart LR
    A["Small server<br/>4 CPU · 8 GB RAM"] -->|upgrade hardware| B["Bigger server<br/>16 CPU · 16 GB RAM"]
```

| ✅ Advantages | ❌ Disadvantages |
| --- | --- |
| Easy | Expensive |
| | Hardware limits |

### Horizontal Scaling — add more servers

```mermaid
flowchart TD
    U[Users] --> LB[Load Balancer]
    LB --> S1[Server 1]
    LB --> S2[Server 2]
    LB --> S3[Server N]
```

| ✅ Advantages | ❌ Disadvantages |
| --- | --- |
| Highly scalable | More moving parts |
| Fault tolerant | |

---

## 6. Microservices Architecture

Instead of one huge application, split it into independent services.

```mermaid
flowchart TD
    M[Mobile App] --> GW[API Gateway]
    GW --> US[User Service]
    GW --> OS[Order Service]
    GW --> PS[Payment Service]
    GW --> NS[Notification Service]
```

| ✅ Advantages | ❌ Disadvantages |
| --- | --- |
| Independent deployment | Higher complexity |
| Clear team ownership | Network communication overhead |
| Better scalability | Harder to monitor |

---

## 7. Authentication

**Authentication:** *Who you are.* **Authorization:** *What you can do.*

```mermaid
flowchart TD
    A[Login] --> B[Verify credentials]
    B --> C[Issue access token]
    C --> D[Access protected APIs]
```

**Modern auth stack — refreshing an expired token:**

```mermaid
flowchart TD
    A[Mobile App] --> B[Send access token]
    B --> C{Token expired?}
    C -->|No| D[API responds]
    C -->|Yes| E[Send refresh token]
    E --> F[Issue new access token]
    F --> B
```

---

## 8. File Serving

Files can be images, videos, PDFs, or documents.

### ❌ Bad approach — store files inside the API server

```mermaid
flowchart TD
    E[Express Server] --> API[API]
    E --> IMG[Images]
```

> Heavy traffic can overload the API server.

### ✅ Better approach — serve through a CDN + object storage

```mermaid
flowchart TD
    U[User] --> CDN[CDN]
    CDN --> OS[Object Storage]
```

**Examples:** Amazon S3 · Cloudflare R2 · Google Cloud Storage

**Typical upload flow:**

```mermaid
flowchart TD
    A[Mobile App] --> B[Backend]
    B --> C[Generate signed upload URL]
    C --> D[Client uploads directly to storage]
```

---

## 9. Event Broker

An event broker lets services communicate **asynchronously**.

### Without a broker

```mermaid
flowchart LR
    O[Order Service] --> N[Notification Service]
```

> If the notification fails, the order may fail too.

### With a broker

```mermaid
flowchart LR
    O[Order Service] --> B[Event Broker] --> N[Notification Service]
```

**Popular brokers:** RabbitMQ · Apache Kafka · Redis Pub/Sub

**Example — one event, many consumers:**

```mermaid
flowchart TD
    A[Order Created] --> K[Kafka Topic]
    K --> E[Email Service]
    K --> AN[Analytics Service]
    K --> I[Inventory Service]
```

---

## 10. Caching and CDNs

**Caching:** store frequently accessed data close to the application.

### Without cache — every request hits the database

```mermaid
flowchart LR
    R[Request] --> DB[(Database)]
```

### With cache — database is only hit on a miss

```mermaid
flowchart LR
    R[Request] --> C{Cache hit?}
    C -->|Hit| RES[Return cached data]
    C -->|Miss| DB[(Database)]
    DB --> C
```

**Popular cache:** Redis

### CDN — store files near users globally

```mermaid
flowchart TD
    O[Origin Server] --> CDN[CDN]
    CDN --> U[Users Worldwide]
```

**Benefits:** faster images · lower server load · global performance

**Popular CDNs:** Cloudflare · Amazon CloudFront · Fastly

---

## 11. Rate Limiting

Prevents abuse and protects servers by capping how many requests a client can make.

```mermaid
flowchart TD
    R[Incoming request] --> C{Within limit?<br/>e.g. 100 req/min}
    C -->|Yes| OK[200 OK — process request]
    C -->|No| NO[429 Too Many Requests]
```

---

## 12. How Everything Fits Together

A modern production backend often looks like this:

```mermaid
flowchart TD
    M[Mobile App] --> N[Nginx / Load Balancer]
    N --> API[API Servers]

    API --> AUTH[Authentication]
    API --> CACHE[(Cache · Redis)]
    API --> DB[(Database)]

    API --> EB[Event Broker]
    EB --> W[Email / Analytics / Notifications]

    API --> OBJ[Object Storage]
    OBJ --> CDN[CDN]
    CDN --> U[Users Worldwide]
```
