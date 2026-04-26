# Lab 1: Cloud Virtualization and Data Center Architecture

**Environment:** Ubuntu 22.04 (VirtualBox VM) + Docker v29.4.1 + Apache Bench

---

## Learning Objectives
1. Understand the difference between VMs and containers in practice
2. Deploy and compare instances on local environments
3. Analyze tail latency patterns in a simulated web service
4. Experiment with virtualization overhead and container density

---

## Tools Used
- Ubuntu 22.04 LTS (VirtualBox VM on Windows 11)
- Docker v29.4.1
- Apache Bench (ab) for load testing
- Flask (Python) for web service simulation

---

## Part A: VMs vs Containers

| Metric | Ubuntu VM | Docker Container |
|--------|-----------|-----------------|
| Startup time | ~30-60 seconds | ~3 seconds |
| Running processes | 200+ | 2 (bash + ps) |
| Memory used | ~1.2 GB | ~50 MB |
| Disk layout | Full filesystem | Simple overlay |

---

## Part B: Virtualization Detection

```bash
sudo dmidecode | grep -A3 "System Information"
dmesg | grep -i nitro
```

Results:
- dmidecode: Manufacturer: innotek GmbH, Product: VirtualBox
- dmesg: Operation not permitted (kernel buffer protected)

Same behavior as AWS EC2 Nitro - hypervisor visible but kernel locked.

---

## Part C: Tail Latency Results

| Percentile | Native Flask | Docker Flask |
|-----------|-------------|-------------|
| 50% | 83 ms | 63 ms |
| 90% | 240 ms | 218 ms |
| 95% | 414 ms | 271 ms |
| 99% | 671 ms | 613 ms |
| Req/sec | 63.66 | 61.23 |
| Failed | 0 | 0 |

---

## Simple Benchmark (No Delay)

| Metric | Native | Docker |
|--------|--------|--------|
| Requests/sec | 673.49 | 579.67 |
| Mean time | 14.848 ms | 17.251 ms |
| p99 | 25 ms | 28 ms |

---

## Deliverables
- [x] app.py
- [x] Dockerfile
- [x] native_results.txt
- [x] docker_results.txt
- [x] tail_latency_results.txt
- [x] tail_latency_docker_results.txt
- [x] reflection.txt
