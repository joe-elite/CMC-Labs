# Lab 02 - Distributed Consistency and Consensus in the Cloud

**Student:** Youssef Adel - SE2
**Course:** Cloud and Mobile Computing (CMC)

---

## Overview

This lab explores distributed systems consistency, availability,
and partition tolerance using Redis and etcd. We observe how
different systems behave under the CAP theorem in practice.

---

## Environment

- OS: Ubuntu 22.04 (VirtualBox VM)
- Docker: 29.4.1
- Docker Compose: v5.1.3

---

## Folder Structure
Lab_02/
├── Task_1_Redis/
│   ├── commands.txt       # All Redis commands with real outputs
│   └── reflection.txt     # Analysis and CAP theorem discussion
├── Task_2_etcd/
│   ├── commands.txt       # All etcd commands with real outputs
│   └── reflection.txt     # Raft consensus analysis and comparison
├── screenshots/           # Terminal screenshots from experiments
├── docker-compose.yml     # Compose file for all containers
└── README.md


---

## Setup

All containers started with:

```bash
docker compose up -d
```

Containers running:
- lab_02-redis-node1-1 (port 7001)
- lab_02-redis-node2-1 (port 7002)
- lab_02-etcd-1 (port 2379)

---

## Task 1: Redis Replication and CAP Theorem

**Goal:** Observe how standalone Redis nodes behave
under the CAP theorem during normal operation and
simulated network partition.

**Key Findings:**
- Writing to Node 1 was NOT replicated to Node 2
- Node 2 returned (nil) for all keys written to Node 1
- Node 1 stayed fully available during Node 2 outage
- No automatic sync occurred after Node 2 restarted
- Redis in default mode is an AP system

**CAP Result:** AP - Availability + Partition Tolerance

---

## Task 2: Raft Consensus with etcd

**Goal:** Observe how etcd uses the Raft consensus
algorithm to guarantee strong consistency and
data durability across restarts.

**Key Findings:**
- All writes immediately confirmed by the leader
- Endpoint status showed node as active leader
- Stopping etcd caused complete unavailability
- All data persisted perfectly after restart
- etcd prioritizes consistency over availability

**CAP Result:** CP - Consistency + Partition Tolerance

---

## Redis vs etcd - CAP Comparison

| Property     | Redis (default) | etcd (Raft)         |
|--------------|-----------------|---------------------|
| CAP Type     | AP              | CP                  |
| Consistency  | Eventual (none) | Strong              |
| Availability | Always          | Needs majority      |
| Data Sync    | None            | Automatic via Raft  |
| Best For     | Cache, sessions | Config, discovery   |

---

## Conclusion

This lab demonstrated that no distributed system can
guarantee Consistency, Availability, and Partition
Tolerance simultaneously. Redis chooses availability
making it ideal for caching. etcd chooses consistency
making it ideal for critical configuration storage.
