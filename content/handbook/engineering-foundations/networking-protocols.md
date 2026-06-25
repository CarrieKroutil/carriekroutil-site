---
title: "Networking & Protocols"
weight: 40
description: "How HTTP evolved across HTTP/1.1, /2, and /3, and how the common communication protocols — REST, gRPC, WebSocket, AMQP, TCP, UDP — actually differ."
lastUpdated: 2026-06-25
goDeeper:
  - group: Books
    title: "Computer Networking: A Top-Down Approach — Kurose & Ross"
    url: "https://gaia.cs.umass.edu/kurose_ross/index.php"
    why: "The standard networking text, taught from the application layer down — the one to read if you want the whole stack to click."
  - group: Tools
    title: "MDN — HTTP reference"
    url: "https://developer.mozilla.org/en-US/docs/Web/HTTP"
    why: "The clearest practical reference for methods, status codes, headers, and caching — bookmark it."
  - group: Tools
    title: "IETF HTTP Working Group"
    url: "https://httpwg.org/"
    why: "The source specs for HTTP/1.1 through HTTP/3, including QUIC — for when you need the authoritative answer."
---

Most of what an engineering team builds talks to something else over a network, so a working grasp of the protocols pays for itself in clearer design reviews and faster incident calls. You don't need to memorize the specs — you need to know what each protocol is *for* and what it trades away. This page covers how HTTP has evolved and how the common communication styles compare.

## HTTP: /1.1 vs /2 vs /3

HTTP is the foundation of the web, and it has evolved across three major versions that are all still in use. The headline: each version chips away at latency, and HTTP/3 finally changes the transport underneath.

| Version | Released | Transport | Key improvements | Used today? |
|--------------|----------|-------------------|-------------------------------------------------------------|-------------|
| **HTTP/1.1** | 1997 | TCP | Keep-alive connections, chunked transfers | Still common |
| **HTTP/2** | 2015 | TCP | Multiplexing, header compression, binary framing, server push | Widely adopted |
| **HTTP/3** | 2022 | QUIC over UDP | Eliminates TCP head-of-line blocking, encrypted by default | Growing fast |

**HTTP/1.1 — the original workhorse.** Text-based, roughly one request per connection. It introduced keep-alive connections and chunked transfer encoding, but it suffers from **head-of-line blocking**: one slow request can hold up everything behind it. Still widely used, especially in older systems.

**HTTP/2 — faster, binary, smarter.** Still on TCP, but adds **multiplexing** (many requests over one connection), **HPACK** header compression, **binary framing** for efficient parsing, and **server push**. The catch: because it still rides on TCP, it inherits TCP's head-of-line blocking.

**HTTP/3 — built for speed with QUIC.** Runs on **QUIC**, a protocol built on UDP. It handles streams independently (so no head-of-line blocking), is **encrypted by default** via TLS 1.3, sets up connections faster with a **0-RTT handshake**, and survives network switching — say, moving from Wi-Fi to mobile mid-request. Supported by Chrome, Firefox, Safari, Cloudflare, and modern CDN stacks.

Side by side:

| Feature | HTTP/1.1 | HTTP/2 | HTTP/3 |
|-----------------------------|----------------|-------------------|------------------------|
| **Transport** | TCP | TCP | QUIC (over UDP) |
| **Multiplexing** | No | Yes | Yes |
| **Header compression** | No | HPACK | QPACK |
| **Server push** | No | Yes | Limited |
| **Encryption required** | Optional | Optional | Always (TLS 1.3) |
| **Fixes HOL blocking** | No | No (TCP-bound) | Yes |
| **Connection speed** | Slow | Fast | Faster |

Learn more: [HTTP/2](https://developers.cloudflare.com/http/http2/) and [HTTP/3](https://developers.cloudflare.com/http/http3/) overviews from Cloudflare, [QUIC on Wikipedia](https://en.wikipedia.org/wiki/QUIC), and the [IETF HTTP Working Group](https://httpwg.org/).

{{< protip >}}
You rarely choose your HTTP version by hand — your CDN, load balancer, and client negotiate it. The thing worth knowing as a manager: enabling HTTP/2 or /3 is usually a config change at the edge with real latency wins, not an application rewrite. When someone proposes a big performance project, "did we turn on HTTP/3 first?" is a fair question.
{{< /protip >}}

## Communication protocols

Beyond plain HTTP, distributed systems use a handful of communication styles. They all ultimately ride on TCP or UDP, but they differ in shape — request/response versus streaming, one-way versus bidirectional, real-time versus queued.

| Style | What it is | Direction | Real-time? | Streaming? | Best for | Year |
|-----------|----------------------------------|--------------------------|-------------|------------|----------------------------------|------|
| **HTTP** | Foundation of web communication | One-time request | No | No | Web browsers, basic APIs | 1991 |
| **REST** | HTTP API style | One-time request | No | No | Web services, CRUD APIs | 2000 |
| **SOAP** | XML-based enterprise protocol | One-time request | No | No | Legacy & enterprise APIs | 1998 |
| **GraphQL** | Query language for APIs | One-time, dynamic | No | No | Flexible frontends, modern APIs | 2015 |
| **RPC** | Remote command style | One-time or streaming | Some | Yes | Microservices, internal APIs | 1984 |
| **gRPC** | Modern RPC over HTTP/2 | One-time or streaming | Yes | Client/server/bidirectional | Low-latency microservice comms | 2015 |
| **WebSocket** | Real-time 2-way channel | Full-duplex | Yes | Yes | Chat, games, live dashboards | 2011 |
| **AMQP** | Message-queue protocol | Asynchronous | No | No | Background jobs, IoT, logs | 2003 |
| **TCP** | Reliable transport protocol | Two-way | Low-level | Yes | Moving any kind of data | 1974 |
| **UDP** | Lightweight, unreliable transport | One-way (fire-and-forget) | Super fast | No | Games, video, voice, IoT | 1980 |

A few anchors for the mental model: **REST, SOAP, and GraphQL all ride on HTTP**; **gRPC** runs over HTTP/2 with binary messages and is the go-to for low-latency service-to-service calls; **WebSocket** keeps a connection open for true two-way streaming; **AMQP** decouples sender and receiver through a broker, which is what you want for background work. Underneath, **TCP** guarantees ordered, reliable delivery while **UDP** trades those guarantees for raw speed — which is exactly why real-time video and games use it.

### Direction modes

It also helps to name the directionality, because it shows up in protocol choices:

| Mode | Meaning | Analogy | Examples |
|------------------|--------------------------------------|----------------|----------------------------------|
| **Simplex** | One-way only | TV broadcast | HTTP, REST, SOAP |
| **Half-duplex** | Two-way, one side at a time | Walkie-talkie | Legacy polling systems |
| **Full-duplex** | Two-way, simultaneous | Phone call | WebSocket, gRPC streaming, SignalR |

{{< protip >}}
The most common protocol mistake I see is reaching for WebSockets or gRPC streaming when plain request/response would do. Persistent connections cost you in load balancing, reconnection logic, and observability. Start with REST; move to streaming only when the use case is genuinely real-time and bidirectional — live collaboration, trading, gameplay — not just "we want it to feel fast."
{{< /protip >}}
