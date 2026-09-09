# Monitoring Lab — Prometheus, Grafana & ELK

A containerized monitoring and centralized logging practical built with Docker Compose on WSL Ubuntu.

## Overview

This project demonstrates end-to-end observability by combining system monitoring with centralized logging. It includes Prometheus for metrics collection, Grafana for visualization and alerting, and the ELK stack (Elasticsearch, Logstash, Kibana) for log aggregation and analysis.

## Architecture

<img width="576" height="387" alt="Architecture Diagram" src="https://github.com/user-attachments/assets/08e23a73-ee09-4371-821d-191c50303ecf" />

### Technology Stack

| Component | Purpose | Port |
|-----------|---------|------|
| Node Exporter | System metrics collection | 9100 |
| Prometheus | Metrics storage & time-series database | 9091 |
| Grafana | Monitoring dashboard & alerting | 3000 |
| Filebeat | Linux log collection & forwarding | — |
| Logstash | Log processing & transformation | 5044 |
| Elasticsearch | Log storage & indexing | 9200 |
| Kibana | Log visualization & search | 5601 |

## Project Structure

```
monitoring-lab/
├── README.md
├── .gitignore
├── docker-compose.yml
├── prometheus/
│   └── prometheus.yml
├── grafana/
│   └── dashboards/
└── elk/
    ├── filebeat/
    │   └── filebeat.yml
    └── logstash/
        └── pipeline/
            └── logstash.conf
```

## Monitoring Pipeline

### Prometheus

Prometheus scrapes metrics from Node Exporter and itself every 15 seconds.

**Node Exporter target:**
```
http://node-exporter:9100
```

**Prometheus endpoint (from host):**
```
http://localhost:9091
```

### Grafana Dashboard

Grafana connects to Prometheus at:
```
http://prometheus:9090
```

**Dashboard Metrics:**

| Metric | PromQL Query |
|--------|--------------|
| CPU Usage | `100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)` |
| Memory Usage | `100 * (1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)` |
| Disk Usage | `100 * (1 - node_filesystem_avail_bytes{fstype!~"tmpfs\|overlay"} / node_filesystem_size_bytes{fstype!~"tmpfs\|overlay"})` |
| System Uptime | `time() - node_boot_time_seconds` |

## Logging Pipeline

### ELK Stack Configuration

**Log Source:**
```
/var/log/*.log
```

**Filebeat → Logstash:**
```
logstash:5044
```

**Log Index Pattern:**
```
linux-logs-YYYY.MM.dd
```

**Kibana Data View:**
```
linux-logs-*
```

## Quick Start

### 1. Start the Stack

```bash
docker compose up -d
```

### 2. Verify Containers

```bash
docker compose ps
```

### 3. Stop the Stack

```bash
docker compose down
```

## Service URLs

| Service | URL |
|---------|-----|
| Prometheus | http://localhost:9091 |
| Node Exporter | http://localhost:9100 |
| Grafana | http://localhost:3000 |
| Elasticsearch | http://localhost:9200 |
| Kibana | http://localhost:5601 |

## Verification & Testing

### Check Node Exporter Metrics

```bash
curl http://localhost:9100/metrics
```

### Check Elasticsearch Health

```bash
curl http://localhost:9200
```

### View Elasticsearch Indices

```bash
curl "http://localhost:9200/_cat/indices?v"
```

### Test Logging

1. Generate a log entry:
```bash
logger "Monitoring Lab test log - ELK pipeline verification"
```

2. View in Kibana:
   - Navigate to http://localhost:5601
   - Go to **Discover**
   - Select data view: `linux-logs-*`
   - Refresh to see the new log entry

## Alerting

### Grafana Availability Alert

**Alert Rule:**
- Metric: `up{job="node-exporter"}`
- Condition: `Last() < 1`

**Alert States:**
- Node Exporter **available**: `up = 1`
- Node Exporter **unavailable**: `up = 0`

## Learning Outcomes

This practical demonstrates:

- ✓ Docker Compose containerization
- ✓ System metrics collection (Node Exporter)
- ✓ Metrics storage & retrieval (Prometheus)
- ✓ Metrics visualization (Grafana)
- ✓ Log collection (Filebeat)
- ✓ Log processing & transformation (Logstash)
- ✓ Log storage & indexing (Elasticsearch)
- ✓ Log visualization & search (Kibana)
- ✓ System monitoring best practices
- ✓ Centralized logging architecture
- ✓ Alert configuration
- ✓ End-to-end observability

## Observability Pipelines

### Monitoring Pipeline
```
Linux System
    ↓
Node Exporter (metrics)
    ↓
Prometheus (storage)
    ↓
Grafana (visualization)
    ↓
Alerting
```

### Logging Pipeline
```
Linux Logs (/var/log/*.log)
    ↓
Filebeat (collection)
    ↓
Logstash (processing)
    ↓
Elasticsearch (indexing & storage)
    ↓
Kibana (visualization & search)
```

## Technical Details

### Prometheus Configuration
- Scrape interval: 15 seconds
- Targets: Node Exporter, Prometheus (self)

### Filebeat & Logstash
- Input paths: `/var/log/*.log`
- Output: Elasticsearch with daily index rotation

### Grafana
- Default datasource: Prometheus
- Supported alert conditions: Threshold-based with customizable intervals

## Prerequisites

- Docker & Docker Compose installed
- WSL Ubuntu (or any Linux environment)
- 2GB+ available disk space for logs and metrics

## Notes

- All containers communicate via Docker network
- Logs are indexed daily for efficient retrieval
- Prometheus data is ephemeral by default (configure persistent volumes for production)
- Elasticsearch requires adequate memory allocation

## Author

**Monitoring Lab Practical**

Built and tested using Windows, WSL Ubuntu, Docker, and Docker Compose.

---

*Last updated: 2026*
