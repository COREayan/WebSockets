### WebSockets

# 🌐 WebSockets – A Comprehensive Guide

## 📌 Overview

WebSockets provide a **full-duplex, persistent communication channel** over a single TCP connection. Unlike traditional HTTP (request–response), WebSockets enable **real-time, bidirectional data exchange** between clients and servers.

WebSockets are standardized by **RFC 6455** and are widely used for:

* Real-time chat applications
* Live notifications
* Multiplayer games
* Financial tickers
* Collaborative tools

---

## 🔄 HTTP vs WebSockets

| Feature       | HTTP                       | WebSockets  |
| ------------- | -------------------------- | ----------- |
| Communication | Half-duplex                | Full-duplex |
| Connection    | Stateless                  | Persistent  |
| Overhead      | High (headers per request) | Low         |
| Latency       | Higher                     | Very low    |
| Real-time     | ❌ Poor                   | ✅ Excellent|

---

## 🧠 How WebSockets Work

### 1️⃣ Handshake (HTTP Upgrade)

WebSockets start as an HTTP request and then **upgrade** the protocol:

```
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: randomBase64Key
Sec-WebSocket-Version: 13
```

If accepted, the server responds:

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: hashedKey
```

➡️ After this, communication switches to the **WebSocket protocol**.

---

## 🔁 WebSocket Communication Model

* **Persistent connection** stays open
* Both client and server can send messages **independently**
* Messages are sent as **frames**
* Supports:

  * Text frames
  * Binary frames
  * Ping/Pong (heartbeat)
  * Close frames

---

## 🔐 Security

* `ws://` → unsecured WebSocket
* `wss://` → WebSocket over TLS (recommended)

Security best practices:

* Always use **WSS** in production
* Authenticate during handshake (JWT, cookies)
* Validate message payloads
* Protect against DoS and message flooding

---

## 🧱 WebSocket Architecture

```
Client  ⇄  WebSocket Connection  ⇄  Server
```

* Single TCP connection
* No repeated HTTP headers
* Server can push data anytime

---

## 🚀 WebSockets in Backend Frameworks

### 🔹 Spring Framework (Java)

Spring provides first-class WebSocket support:

* `WebSocketHandler`
* `@EnableWebSocket`
* `@ServerEndpoint`

#### With STOMP Messaging

* Built on WebSockets
* Adds:

  * Message broker
  * Topics & queues
  * Pub/Sub model

Common annotations:

* `@MessageMapping`
* `@SendTo`

Used for scalable real-time messaging systems.

---

### 🔹 FastAPI (Python)

FastAPI supports **async WebSockets** natively:

* Handles multiple clients
* Supports disconnections gracefully
* Fully async (`async/await`)

Key features:

* Accept WebSocket connections
* Broadcast to multiple clients
* Detect disconnect events

---

### 🔹 IIS (Microsoft Web Server)

WebSocket support must be **explicitly enabled**:

* Supported since IIS 8
* Works with ASP.NET
* Requires WebSocket protocol feature

Used in enterprise Windows-hosted applications.

---

## 📡 STOMP over WebSockets

STOMP (Simple Text Oriented Messaging Protocol):

* Runs **on top of WebSockets**
* Adds structure & semantics
* Supports:

  * Topics (`/topic`)
  * Queues (`/queue`)
  * Message brokers

Widely used in **Spring Boot real-time apps**.

---

## ⚙️ Handling Disconnections

WebSocket connections can close due to:

* Network failure
* Client refresh
* Server shutdown

Best practices:

* Catch disconnect exceptions
* Remove client from active sessions
* Implement reconnection logic

---

## 📈 Scalability Considerations

WebSockets are **stateful**, which introduces challenges:

* Sticky sessions required
* Use external message brokers (Redis, RabbitMQ)
* Horizontal scaling needs coordination

Solutions:

* STOMP + message broker
* Event-driven architecture
* Load balancer with session affinity

---

## 🧪 Common Use Cases

* 💬 Chat applications
* 📊 Live dashboards
* 🔔 Push notifications
* 🎮 Online games
* 🤝 Collaborative editors

---

## ✅ Advantages

* Real-time communication
* Low latency
* Reduced bandwidth usage
* Efficient for frequent updates

---

## ❌ Limitations

* Stateful connections
* More complex scaling
* Firewall/proxy issues (rare today)

---

## 📚 References
1. https://datatracker.ietf.org/doc/html/rfc6455
2. https://docs.spring.io/spring-framework/reference/web/websocket/server.html
3. https://fastapi.tiangolo.com/advanced/websockets/#handling-disconnections-and-multiple-clients
4. https://www.twilio.com/docs/glossary/what-are-websockets 
5. https://www.wallarm.com/what/a-simple-explanation-of-what-a-websocket-is 
6. https://emily-elim04.medium.com/building-real-time-apps-using-websockets-dc137ccdd34b 
7. https://spring.io/guides/gs/messaging-stomp-websocket 
8. https://learn.microsoft.com/en-us/iis/configuration/system.webserver/websocket 


---

## ✨ Conclusion

WebSockets are the **backbone of modern real-time web applications**. When combined with frameworks like **Spring Boot (STOMP)** or **FastAPI (async)**, they enable scalable, low-latency systems that go far beyond traditional HTTP.


