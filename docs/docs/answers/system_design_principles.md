---
title: 'System Design Principles: Scalability, Reliability, and Performance'
slug: '/answers/system_design_principles'
---

# System Design Principles: Scalability, Reliability, and Performance

System design is the process of defining the architecture, components, modules, interfaces, and data for a system to satisfy specified requirements.

## 1. Requirements Gathering

Before designing any system, it's crucial to understand the constraints:

- **Functional Requirements:** What the system _does_ (e.g., "User can send a message").
- **Non-Functional Requirements:** How the system _performs_ (e.g., "Handle 10k RPS", "99.9% availability", "Low latency").

## 2. Scalability Strategies

### Vertical Scaling (Scaling Up)

Increasing the capacity of a single machine (more CPU, RAM, SSD).

- **Pros:** Simple, no architectural changes.
- **Cons:** Limited by hardware capacity, expensive, single point of failure.

### Horizontal Scaling (Scaling Out)

Adding more machines to the pool and distributing the load.

- **Pros:** Virtually unlimited growth, high fault tolerance.
- **Cons:** Requires a Load Balancer and architectural complexity (distributed state).

## 3. Database Scalability

- **Replication:** Copying data across multiple nodes (Master-Slave/Leader-Follower). Writes go to Master, Reads to Slaves.
- **Sharding:** Partitioning data across multiple independent database servers based on a **Sharding Key** (e.g., User ID).
- **Partitioning:** Dividing large tables into smaller logical pieces within the same database instance.

## 4. Performance Metrics & Little's Law

- **Latency:** Time to process a single request.
- **Throughput:** Number of requests handled per unit of time.
- **Concurrency:** Number of requests being processed simultaneously.

**Little's Law:** `Concurrency = Throughput × Latency`.
If you increase parallel requests (concurrency) without increasing throughput, latency will increase.

## 5. Resilience Patterns

### Circuit Breaker

Prevents cascading failures by "breaking" the connection to a failing dependency and returning a fast fallback.

### Back Pressure

The system rejects new requests (e.g., HTTP 429) when it's overloaded, instead of letting queues grow indefinitely and crashing.

### Idempotency

Ensures that multiple identical requests have the same effect as a single one. Critical for retrying failed network requests (e.g., payments).

## 6. Reducing Tail Latency (P99)

Tail latency refers to the high latency experienced by a small percentage of requests.

- **Hedged Requests:** Sending the same request to multiple replicas and using the first response.
- **Request Coalescing:** Combining identical concurrent requests into one backend call.
- **Jittering:** Adding random delays to scheduled tasks to prevent "thundering herd" problems.
- **Deadline Propagation:** Passing a maximum time limit to all sub-services.

## 7. Distributed Transactions

- **2PC (Two-Phase Commit):** Synchronous, blocking, ensures strict consistency but hard to scale.
- **Saga Pattern:** A sequence of local transactions with compensating actions for failures. Provides eventual consistency and high scalability.
- **TCC (Try-Confirm-Cancel):** Reservation-based pattern common in fintech.

## 8. Consistency Models

- **Strong Consistency:** Every read sees the most recent write (e.g., banking).
- **Eventual Consistency:** Replicas will eventually converge, but may temporarily show old data (e.g., social media likes).
- **Linearizability:** Operations appear instantaneous and ordered.
