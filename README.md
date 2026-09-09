# Monitoring Lab — Prometheus, Grafana & ELK

A containerized monitoring and centralized logging practical built with Docker Compose on WSL Ubuntu.

## Architecture
<img width="576" height="387" alt="image" src="https://github.com/user-attachments/assets/08e23a73-ee09-4371-821d-191c50303ecf" />

Technologies
Component	Purpose	Port
Node Exporter	System metrics	9100
Prometheus	Metrics collection and storage	9091
Grafana	Monitoring dashboard and alerting	3000
Filebeat	Linux log collection	—
Logstash	Log processing	5044
Elasticsearch	Log storage and indexing	9200
Kibana	Log search and visualization	5601
Docker Compose	Container orchestration	—
Project Structure
monitoring-lab/
├── README.md
├── .gitignore
├── docker-compose.yml
├── prometheus/
│   └── prometheus.yml
├── grafana/
└── elk/
    ├── filebeat/
    │   └── filebeat.yml
    └── logstash/
        └── pipeline/
            └── logstash.conf
Prometheus

Prometheus scrapes itself and Node Exporter every 15 seconds.

Node Exporter target:

node-exporter:9100

Prometheus is available from the host at:

http://localhost:9091
Grafana Dashboard

Grafana connects to Prometheus using:

http://prometheus:9090

Dashboard panels:

CPU Usage
100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
Memory Usage
100 * (1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)
Disk Usage
100 * (1 - node_filesystem_avail_bytes{fstype!~"tmpfs|overlay"} / node_filesystem_size_bytes{fstype!~"tmpfs|overlay"})
System Uptime
time() - node_boot_time_seconds
ELK Stack

Linux logs are collected from:

/var/log/*.log

Filebeat forwards logs to:

logstash:5044

Logstash sends the events to Elasticsearch.

Log indices use:

linux-logs-YYYY.MM.dd

Kibana uses the Data View:

linux-logs-*
Running the Project

Start the stack:

docker compose up -d

Check the containers:

docker compose ps

Stop the stack:

docker compose down
Service URLs
Prometheus: http://localhost:9091
Node Exporter: http://localhost:9100
Grafana: http://localhost:3000
Elasticsearch: http://localhost:9200
Kibana: http://localhost:5601
Verification

Check Node Exporter:

curl http://localhost:9100/metrics

Check Elasticsearch:

curl http://localhost:9200

Check Elasticsearch indices:

curl "http://localhost:9200/_cat/indices?v"

Check containers:

docker compose ps
Log Testing

Generate a Linux log event:

logger "Monitoring Lab test log - ELK pipeline verification"

Then open Kibana:

http://localhost:5601

Go to Discover, select:

linux-logs-*

and refresh the results.

Alerting

A basic Grafana availability alert can use:

up{job="node-exporter"}

Condition:

Last() is below 1

When Node Exporter is available:

up = 1

When Node Exporter is unavailable:

up = 0
Evidence Screenshots

The practical includes screenshots demonstrating:

Architecture
Prometheus targets
Grafana monitoring dashboard
Kibana Discover with Linux logs
Grafana alert configuration
Learning Outcomes

This practical demonstrates:

Docker Compose
Node Exporter
Prometheus
Grafana
Filebeat
Logstash
Elasticsearch
Kibana
System monitoring
Centralized logging
Basic alerting
End-to-end observability
Final Pipelines
Monitoring
Linux System
    ↓
Node Exporter
    ↓
Prometheus
    ↓
Grafana
    ↓
Alerting
Logging
Linux Logs
    ↓
Filebeat
    ↓
Logstash
    ↓
Elasticsearch
    ↓
Kibana
Author

Monitoring Lab Practical

Built and tested using Windows, WSL Ubuntu, Docker, and Docker Compose.
