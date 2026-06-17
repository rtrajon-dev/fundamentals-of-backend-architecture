<div align="center">

# Fundamentals of Backend Architecture,

**Rajon Talukdar**

_June 17, 2026_

</div>

---

## 1. Stateful Servers!

A stateful server **remembers information** about a user **between requests**. The Server **stores session data in RAM**.

```
User Login
   ↓
Server stores session in memory
   ↓
User requests profile
   ↓
Server remembers user from session
```

**Advantages**

- Easy implementation
- Traditional web applications

**Disadvantages**

- Difficult to scale
- If server crashes, session may be lost
- Load balancer must send user to same server

---

## 2. Stateless Servers!

A stateful server **stores no information** about a user **between requests**. Each request contains everything needed..

```
Login
   ↓
Server generates JWT
   ↓
Client stores JWT
   ↓
Client sends JWT on every request
```

**Advantages**

- Easy horizontal scaling
- Better for mobile apps
- Cloud-friendly

**Disadvantages**

- Token management required

---

## 3. Load Balancing!

When one **Server cannot handle all traffic**, **requests** are **distributed** across **multiple servers**.

```
Users
   ↓
Load balancer
   ↓  ~  ↓  ~  ↓
Server1 ~ Server2 ~ Server3
```

**Responsibilities**

- Distribute traffic
- Health checks
- Failover
- SSL termination

**Popular load balancers**

- Nginx
- HAProxy
- Traefik
- Envoy

---

## 4. Nginx Example!

Nginx is commonly used as: **Reverse proxy**, **Load balancer**, **Static file server**.

```
Client
   ↓
Ngnix
   ↓
Node.js App
```

**Example:**

```nginx
Upstream backend {
        Server 10.0.0.1:3000;
        Server 10.0.0.2:3000;
        Server 10.0.0.3:3000;
}
Server {
        Listen 80;
        Location / {
                Proxy_pass http://backend;
        }
}
```

---

## 5. Different Types of Scaling!

**1. Vertical Scaling:** **Increase power** of one **server**.

```
4 CPU to 16 CPU  ||  8gb RAM to 16gb RAM
```

**Advantages**

- Easy

**Disadvantages**

- Expensive
- Hardware limits

**2. Horizontal Scaling:** **Add** more **servers**.

```
Users
   ↓
Load Balancer
   ↓
Multiple Servers
```

**Advantages**

- Highly scalable
- Fault tolerant

---

## 6. Microservices Architecture!

Instead of one huge Application - Split into services.

```
Monolith

User Service | Order Service | Payment Service | Notification Service
```

**Architecture:**

```
Mobile App
   ↓
API Gateway
   ↓
┌─────────────┐
│ User Service│
├─────────────┤
│Order Service│
├─────────────┤
│Payment      │
└─────────────┘
```

**Advantages**

- Independent deployment
- Team ownership
- Better scalability

**Disadvantages**

- Complexity
- Network communication
- Monitoring challenges

---

## 7. Authentication!

Authentication: Who you are. Authorization: What can you do?

```
Login
   ↓
Verify credentials
   ↓
Issue Access Token
   ↓
Access protected APIs
```

**Modern auth stack:**

```
Mobile App
   ↓
Access Token
   ↓
API

Expired?
   ↓
Refresh Token
   ↓
New Access Token
```

---

## 8. File Serving!

File can be: Images, Videos, PDFs, Documents

**Bad Approach:**

Store files inside API server.

```
Express Server
├─ API
└─ Images
```

Happy traffic can overload API server

**Batter Approach:**

```
User
   ↓
CDN
   ↓
Object Storage
```

**Example:**

```
Amazon Web Services S3
Cloudflare R2
Google cloud Storage
```

**Typical upload flow:**

```
Mobile app
   ↓
backend
   ↓
Generate Upload URL
   ↓
Direct Upload to Storage
```

---

## 9. Event Broker!

An event broker allows services to communicate asynchronously

**Without broker:**

```
Order Service
   ↓
Notification Service
```

If notification fail => Order may fail

**With broker:**

```
Order service
   ↓
Event Broker
   ↓
Notification Service
```

**Popular brokers:**

```
RabbitMQ
Apache Kafka
Redis Pub/Sub
```

**Example:**

```
Order Created
   ↓
Kafka Topic
   ↓
Email service | Analytics service | Inventory service
```

---

## 10. Caching and CDNs!

Caching: Store frequently accessed data.

**Without cache:**

```
Request
   ↓
Database
```

Every request hits DB

**With cache:**

```
Request
   ↓
Cache
   ↓
Database (if miss)
```

**Popular cache:**

```
Radis
```

CDN: stores files near users globally

```
Origin server
   ↓
CDN
   ↓
Users Worldwide
```

**Benefits:**

```
Faster images
Lower server load
Global performance
```

**Popular CDNs:**

```
CLoudflare
Amazon Web Services
Fastly
```

---

## 11. Rate Limiting!

Prevents abuse and protects servers

**Example:**

```
100 request per minute = 429 too many requests
```

---

## 12. How everything fits together!

A modern production backend often looks like this:

```
Mobile App
   ↓
Nginx
Load Balancer
   ↓
API Servers
   ↓
┌───────────┬───────────┐
│           │           │
Authentication  Cache  Database
│           (Redis)     │
└───────────┴───────────┘
   ↓
Event Broker
   ↓
Email / Analytics / Notifications
   ↓
Object Storage
   ↓
CDN
```
