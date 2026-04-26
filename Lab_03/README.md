# Lab 03 - Containerization and Cluster Orchestration

**Student:** Youssef Adel - SE2
**Course:** Cloud and Mobile Computing (CMC)

---

## Overview

This lab covers Linux container internals, Docker image
layering, and Kubernetes cluster orchestration using kind.
We observe namespaces, cgroups, image builds, scheduling,
and self-healing behavior in a local cluster.

---

## Environment

- OS: Ubuntu 22.04 (VirtualBox VM)
- Docker: 29.4.1
- kind: v0.23.0
- kubectl: v1.35.4
- Kubernetes: v1.30.0

---

## Folder Structure

Lab_03/
├── lab3/
│   ├── app.py
│   ├── requirements.txt
│   ├── Dockerfile.basic
│   ├── Dockerfile.multistage
│   ├── deployment.yaml
│   ├── service.yaml
│   └── probe-deployment.yaml
├── Part_A/
│   ├── commands.txt
│   └── reflection.txt
├── Part_B/
│   ├── commands.txt
│   └── reflection.txt
├── Part_C/
│   ├── commands.txt
│   └── reflection.txt
├── Part_D/
│   ├── commands.txt
│   └── reflection.txt
├── Part_E/
│   ├── commands.txt
│   └── reflection.txt
├── screenshots/
└── README.md

---

## Part A - Containers, Namespaces, and Cgroups

Explored Linux isolation mechanisms through containers.

**Key Findings:**
- Container hostname was isolated from host hostname
- bash appeared as PID 1 inside container
- Same bash process was PID 22342 on the host
- mount showed overlay filesystem isolation
- CPU hard capped at 50% with --cpus="0.5"
- Memory hard capped at 256MiB with --memory="256m"
- docker stats confirmed: 50.18% CPU, 223.3MiB/256MiB RAM

---

## Part B - Docker Image Layering

Built and compared basic vs multistage Docker images.

**Results:**

| Property      | lab3-basic  | lab3-multi  |
|---------------|-------------|-------------|
| Size          | 199MB       | 184MB       |
| pip layer     | 16.5MB      | 5.51MB      |
| Build stages  | 1           | 2           |
| Build tools   | Kept        | Discarded   |

**Key Findings:**
- Multistage build saved 15MB over basic build
- Layer order matters for cache efficiency
- requirements.txt copied before app.py to maximize caching
- Builder stage completely discarded in final image

---

## Part C - Kubernetes Orchestration with kind

Deployed a 3-replica Flask app on a local kind cluster.

**Key Findings:**
- kind cluster created with Kubernetes v1.30.0
- Both images loaded into kind successfully
- 3/3 pods running and available immediately
- curl / returned "Hello from Lab 3"
- curl /health returned "ok"
- ClusterIP service load balanced across all 3 pods

---

## Part D - Scheduling and Placement

Applied node labels and forced pod placement with nodeSelector.

**Key Findings:**
- node-role=general label applied to control-plane node
- nodeSelector added to deployment.yaml
- Rolling update triggered automatically (zero downtime)
- All new pods correctly placed on labeled node
- Declarative scheduling decouples apps from machine names

---

## Part E - Self-Healing and Health Probes

Observed automatic pod replacement and probe configuration.

**Key Findings:**
- Deleted pod replaced automatically within 6 seconds
- Deployment maintained exactly 3 replicas at all times
- Readiness probe: HTTP GET /health delay=3s period=5s
- Liveness probe: HTTP GET /health delay=10s period=10s
- Both probes confirmed active via kubectl describe pod
- Reconciliation loop ensures desired state is maintained

---

## Reflection Questions Summary

1. Namespaces alone do not guarantee fair resource use
   because they only control visibility not consumption.
   Cgroups are needed to enforce resource limits.

2. Cgroups improve cluster stability by preventing any
   single container from consuming all available CPU or
   memory and starving other workloads.

3. Docker image layering is important for orchestration
   because smaller images mean faster pulls across nodes,
   less storage usage, and reduced attack surface.

4. Kubernetes desired state means the declared
   configuration in YAML files. The control plane
   continuously works to match actual state to desired.

5. Self-healing differs from manual operations because
   it is automatic, continuous, and cluster-wide.
   No human needs to detect or respond to failures.

6. Readiness and liveness probes are not interchangeable.
   Readiness controls traffic routing. Liveness controls
   container restart. Both are needed for full resilience.

7. A single-node kind cluster cannot demonstrate real
   multi-node scheduling decisions. All pods land on
   the same node regardless of labels or affinity rules.
