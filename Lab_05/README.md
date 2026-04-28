# Lab 05 - Local Serverless Event-Driven Image Processing Pipeline

**Student:** Youssef Adel - SE2
**Course:** Cloud and Mobile Computing (CMC)

---

## Overview

This lab implements a local serverless-style event-driven
architecture using only free open-source tools. An image
placed in a local folder triggers an automated processing
pipeline without any cloud services.

---

## Environment

- OS: Ubuntu 22.04 (VirtualBox VM)
- Docker: 29.4.1
- Docker Compose: v5.1.3
- Python: Flask 3.0.3, Pillow 10.4.0, Redis 5.0.8

---

## Architecture

Local /data/input folder
|
v
event-source (watcher.py)
|
v
Redis Stream (events)
|
v
event-router (event_router.py)
|
+-----------------+
|                 |
v                 v
image-resizer          notifier
(/resize endpoint)     (/notify endpoint)
|
v
/data/output/resized_test_image.png

---

## Serverless Concept Mapping

| Serverless Concept  | Local Implementation                        |
|---------------------|---------------------------------------------|
| Event source        | watcher.py monitors /data/input folder      |
| Event bus           | Redis Streams stores and delivers events    |
| Event router        | event_router.py reads and routes events     |
| Function-as-a-Service | Containerized Flask services (functions)  |
| Event destinations  | image-resizer and notifier containers       |
| Cold start          | First request after container restart       |

---

## Folder Structure

Lab_05/
├── lecture5-local-serverless-lab/
│   ├── docker-compose.yml
│   ├── data/
│   │   ├── input/
│   │   └── output/
│   ├── event_source/
│   │   ├── Dockerfile
│   │   ├── requirements.txt
│   │   └── watcher.py
│   ├── router/
│   │   ├── Dockerfile
│   │   ├── requirements.txt
│   │   └── event_router.py
│   └── functions/
│       ├── image_resizer/
│       │   ├── Dockerfile
│       │   ├── requirements.txt
│       │   └── app.py
│       └── notifier/
│           ├── Dockerfile
│           ├── requirements.txt
│           └── app.py
├── commands.txt
├── reflection.txt
└── README.md

---

## How to Run

```bash
cd lecture5-local-serverless-lab
docker compose up --build -d
```

Trigger the pipeline:

```bash
docker compose exec image-resizer python3 -c "
from PIL import Image, ImageDraw
img = Image.new('RGB', (1200, 800), (240, 240, 240))
draw = ImageDraw.Draw(img)
draw.text((50, 50), 'Lecture 5 Local Serverless Lab', fill=(0, 0, 0))
img.save('/data/input/test_image.png')
print('Created /data/input/test_image.png')
"
```

---

## Pipeline Results

| Step | Component | Result |
|------|-----------|--------|
| 1 | event-source | Detected test_image.png, published event |
| 2 | Redis Stream | Stored event with unique event_id |
| 3 | event-router | Received and routed to 2 destinations |
| 4 | image-resizer | Resized 1200x800 to 300x200 in 32.96ms |
| 5 | notifier | Logged notification in 5.42ms |
| 6 | output | resized_test_image.png saved to /data/output |

---

## Cold Start Experiment

| Measurement | Value |
|-------------|-------|
| Warm request time | 0.024459s (24.4ms) |
| First request after restart | 0.046249s (46.2ms) |
| Difference | ~21.8ms slower |
| Reason | Flask initialization on first request after restart |

---

## Key Findings

- Full event-driven pipeline worked end to end locally
- Redis Streams delivered events reliably with no loss
- One event triggered two functions simultaneously (fan-out)
- Functions are loosely coupled through the event format
- Cold start effect observed after container restart
- No cloud services required for the entire pipeline
