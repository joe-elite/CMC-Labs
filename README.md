# CMC-Labs — Cloud and Mobile Computing

**Student:** Youssef Adel — SE2
**Course:** Cloud and Mobile Computing (CMC)
**Institution:** Faculty of Engineering

---

## Repository Overview

This repository contains all lab submissions for the Cloud and
Mobile Computing course. Each lab folder contains source code,
commands with real terminal outputs, reflections, and analysis.

---

## Labs Summary

| Lab | Topic                                      | Tools Used                        | Status   |
|-----|--------------------------------------------|-----------------------------------|----------|
| 01  | Docker Containerization and Performance    | Python, Flask, Docker             | Complete |
| 02  | Distributed Consistency and Consensus      | Docker Compose, Redis, etcd       | Complete |
| 03  | Containerization and Cluster Orchestration | Docker, kind, kubectl             | Complete |
| 04  | Microservices and Cloud-Native Design      | Python, Flask, Docker Compose     | Complete |

---

## Lab 01 — Docker Containerization and Performance

Measured the performance overhead introduced by running a
Flask application inside Docker compared to running it natively.

| Metric              | Native | Docker |
|---------------------|--------|--------|
| Average Latency     | Lower  | Higher |
| Tail Latency (p99)  | Lower  | Higher |
| Isolation           | None   | Full   |
| Portability         | Low    | High   |

**Key topics:** Container overhead, tail latency, performance benchmarking

---

## Lab 02 — Distributed Consistency and Consensus

Investigated CAP theorem behavior using two different distributed
systems and observed how each makes trade-offs under partition.

| Property        | Redis (default)   | etcd (Raft)         |
|-----------------|-------------------|---------------------|
| CAP Type        | AP                | CP                  |
| Consistency     | Eventual (none)   | Strong              |
| Availability    | Always            | Needs majority      |
| Data Sync       | None              | Automatic via Raft  |
| Best Use Case   | Cache, sessions   | Config, discovery   |

**Key topics:** CAP theorem, AP vs CP systems, Raft consensus

---

## Lab 03 — Containerization and Cluster Orchestration

Explored Linux isolation mechanisms, Docker image layering,
and Kubernetes orchestration on a local kind cluster.

| Part   | Topic                        | Key Finding                                  |
|--------|------------------------------|----------------------------------------------|
| A      | Namespaces and cgroups       | PID 1 in container = PID 22342 on host       |
| A      | Resource limits              | CPU capped at 50.18%, RAM at 223.3/256 MiB   |
| B      | Image layering               | Multistage build saved 15MB (199 vs 184 MB)  |
| C      | Kubernetes deployment        | 3 replicas running, service load balanced    |
| D      | Scheduling and placement     | nodeSelector forced placement via labels     |
| E      | Self-healing                 | Deleted pod replaced automatically in 6s     |

**Key topics:** Namespaces, cgroups, image layering, scheduling, self-healing

---

## Lab 04 — Microservices and Cloud-Native Design

Built a mini e-commerce backend using two independent microservices
and observed failure propagation and graceful degradation behavior.

| Scenario                    | Behavior                                      |
|-----------------------------|-----------------------------------------------|
| Both services running       | Orders created successfully with correct total|
| product-service stopped     | HTTP 503 returned after retry exhaustion      |
| product-service restarted   | Full recovery with no manual reconfiguration  |

| 12-Factor Principle | Implementation                                  |
|---------------------|-------------------------------------------------|
| III - Config        | PRODUCT_SERVICE_URL via environment variable    |
| VI - Processes      | Stateless services, no local disk state         |
| VII - Port Binding  | Services self-contained on ports 5001 and 5002  |
| IX - Disposability  | Fast startup, restart: unless-stopped policy    |
| X - Dev/Prod Parity | Docker ensures identical environments           |

**Key topics:** Microservices, resilience, health checks, failure injection

---

## Environment

| Component       | Version          |
|-----------------|------------------|
| Host OS         | Windows 11       |
| VM              | Ubuntu 22.04     |
| RAM (VM)        | 8 GB allocated   |
| Docker          | 29.4.1           |
| Docker Compose  | v5.1.3           |
| kind            | v0.23.0          |
| kubectl         | v1.35.4          |
| Kubernetes      | v1.30.0          |
| Python          | 3.14.4           |
| Git             | 2.53.0           |
