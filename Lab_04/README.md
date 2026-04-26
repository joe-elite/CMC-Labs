# Lab 04 - Microservices and Cloud-Native Design

**Student:** Youssef Adel - SE2
**Course:** Cloud and Mobile Computing (CMC)

---

## Overview

This lab implements a mini e-commerce backend using two
microservices and Docker Compose. We observe service-to-service
communication, failure propagation, graceful degradation,
and cloud-native design principles in practice.

---

## Environment

- OS: Ubuntu 22.04 (VirtualBox VM)
- Docker: 29.4.1
- Docker Compose: v5.1.3
- Python: Flask 3.0.3

---

## Architecture
┌─────────────────┐         ┌─────────────────┐
│  order-service  │─────────│ product-service  │
│   port 5002     │  HTTP   │   port 5001      │
│                 │ GET     │                  │
│ POST /orders    │────────>│ GET /products/id │
│ GET  /health    │         │ GET /health      │
└─────────────────┘         └─────────────────┘
---

## Folder Structure
Lab_04/
├── week4-lab/
│   ├── docker-compose.yml
│   ├── product-service/
│   │   ├── app.py
│   │   ├── requirements.txt
│   │   └── Dockerfile
│   └── order-service/
│       ├── app.py
│       ├── requirements.txt
│       └── Dockerfile
├── screenshots/
├── commands.txt
├── reflection.txt
└── README.md
---

## Services

### product-service (port 5001)
- Returns product data from an in-memory catalog
- Endpoints: GET /health, GET /products/<id>
- Products: Laptop ($1200), Phone ($650), Headphones ($120)

### order-service (port 5002)
- Receives order requests and calls product-service
- Implements retry logic (2 retries, 1s delay, 2s timeout)
- Reads PRODUCT_SERVICE_URL from environment variable
- Endpoints: GET /health, POST /orders

---

## How to Run

```bash
cd week4-lab
docker compose up --build -d
docker compose ps
```

---

## API Testing

### Product Service
```bash
curl http://localhost:5001/health
curl http://localhost:5001/products/1
curl http://localhost:5001/products/99
```

### Order Service
```bash
curl -X POST http://localhost:5002/orders \
  -H "Content-Type: application/json" \
  -d '{"product_id": 1, "quantity": 2}'
```

Expected response:
```json
{
  "message": "Order created",
  "product": "Laptop",
  "quantity": 2,
  "total_price": 2400
}
```

---

## Failure Testing Results

| Scenario | Result |
|----------|--------|
| Both services running | Orders created successfully |
| product-service stopped | HTTP 503 after retry exhaustion |
| product-service restarted | Full recovery, orders resume |

---

## Key Findings

- Independent deployment: each service has its own Dockerfile
- Graceful degradation: 503 returned instead of crash
- Environment variables used for service discovery
- Health checks confirm container readiness
- Retry logic handles transient network failures
- Recovery is automatic after service restart

---

## 12-Factor Principles Demonstrated

| Factor | Implementation |
|--------|---------------|
| III - Config | PRODUCT_SERVICE_URL via environment variable |
| VI - Processes | Stateless services, no local disk state |
| VII - Port Binding | Services self-contained on ports 5001/5002 |
| IX - Disposability | Fast startup, restart: unless-stopped policy |
| X - Dev/Prod Parity | Docker ensures identical environments |
