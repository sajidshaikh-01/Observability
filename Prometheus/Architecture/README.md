<img width="1280" height="720" alt="image" src="https://github.com/user-attachments/assets/ccce7639-3c61-496e-8b45-9ac0e5b1d6d1" />

# Prometheus Architecture – Detailed Explanation & Workflow 

This is written in **SRE / DevOps interview + real production language**.

---

## High-Level Idea

Prometheus is a **metrics-based monitoring system** that:

* Discovers targets automatically
* Pulls metrics from them
* Stores metrics as time-series data
* Evaluates alert rules
* Sends alerts to Alertmanager
* Serves data to Grafana / Web UI via PromQL

---

## 1. Applications (Apps)

### What they are

These are your:

* Microservices
* APIs
* Backend services
* Jobs

### What they do

Applications expose metrics in Prometheus format at:

```text
/metrics
```

Example metrics:

* Request count
* Latency
* Error rate

Applications **do not send metrics directly** to Prometheus.

---

## 2. Exporters

### What are Exporters

Exporters are small services that **convert system or app metrics into Prometheus format**.

Prometheus **cannot read OS or container metrics directly**.

### Common Exporters

* Node Exporter → CPU, memory, disk
* kube-state-metrics → Kubernetes object state
* cAdvisor → Container metrics

Exporters also expose metrics on:

```text
/metrics
```

---

## 3. Push Gateway

### Why Push Gateway Exists

Prometheus is **pull-based**, but some workloads:

* Batch jobs
* Cron jobs
* Short-lived jobs

finish before Prometheus can scrape them.

### What Push Gateway Does

* Jobs **push metrics** to Push Gateway
* Prometheus **pulls metrics** from Push Gateway

⚠️ Push Gateway is **not recommended for long-running services**.

---

## 4. Service Discovery

Service Discovery tells Prometheus:

> **Which targets should I scrape?**

### Types Shown in Diagram

#### a. Kubernetes Service Discovery

* Discovers pods, services, nodes dynamically
* Uses labels and annotations

#### b. Consul

* Discovers services registered in Consul

#### c. File-based Service Discovery

* Reads targets from static files
* Mostly used for legacy systems

Service Discovery **updates targets automatically** when services scale up/down.

---

## 5. Prometheus Server (Core Component)

This is the **heart of the architecture**.

### 5.1 Metrics Retrieval Engine

* Periodically scrapes `/metrics` endpoints
* Uses HTTP
* Controlled by `scrape_interval`

Prometheus **pulls metrics**, targets never push.

---

### 5.2 TSDB (Time Series Database)

* Stores metrics as time-series data
* Each data point has:

  * Metric name
  * Labels
  * Value
  * Timestamp

Optimized for:

* High write throughput
* Fast queries

---

### 5.3 Node Local Storage

* Metrics are stored locally on disk
* Prometheus is **single-node storage by default**

For long-term storage & HA, production uses:

* Thanos
* Cortex

---

### 5.4 HTTP Server

Prometheus exposes an HTTP server that provides:

* Prometheus Web UI
* PromQL query endpoint
* Metrics API

Grafana and other tools query Prometheus via this HTTP server.

---

## 6. PromQL (Prometheus Query Language)

PromQL is used to:

* Query metrics
* Aggregate data
* Calculate rates
* Define alert rules

Example:

```text
rate(http_requests_total[5m])
```

PromQL queries run **inside the Prometheus server**.

---

## 7. Alerting Rules (Inside Prometheus)

Prometheus continuously evaluates alert rules:

Example:

```text
CPU usage > 80% for 5 minutes
```

If condition is met:

* Alert fires
* Sent to Alertmanager

Prometheus **does not notify users directly**.

---

## 8. Alertmanager

### Role of Alertmanager

Alertmanager is responsible for:

* Receiving alerts from Prometheus
* Grouping similar alerts
* Deduplicating alerts
* Routing alerts to receivers

### Receivers Shown

* Slack
* Email
* XMatters

Alertmanager prevents **alert storms**.

---

## 9. Visualization Layer

### Prometheus Web UI

* Used for quick debugging
* Not used heavily in production

### Grafana

* Main visualization tool
* Uses Prometheus as a data source
* Provides dashboards for:

  * Nodes
  * Pods
  * Services
  * SLIs / SLOs

### API Consumers

* External systems
* Autoscalers
* Custom tools

---

## End-to-End Workflow (Step by Step)

### Normal Metrics Flow

1. Applications / Exporters expose `/metrics`
2. Service Discovery updates target list
3. Prometheus scrapes targets periodically
4. Metrics stored in TSDB
5. PromQL queries analyze data
6. Grafana visualizes metrics

---

### Batch Job Workflow

1. Batch job finishes execution
2. Job pushes metrics to Push Gateway
3. Prometheus scrapes Push Gateway
4. Metrics stored in TSDB

---

### Alerting Workflow

1. Prometheus evaluates alert rules
2. Alert condition matches
3. Prometheus sends alert to Alertmanager
4. Alertmanager groups & deduplicates
5. Alert sent to Slack / Email / XMatters

---

## Key Production Takeaways

* Prometheus is **pull-based**
* Exporters expose metrics, Prometheus never reads OS directly
* Push Gateway is only for short-lived jobs
* Alertmanager handles notification logic
* Grafana is used for dashboards
* Prometheus storage is local by default

---

## Interview One-Liner

> Prometheus is a pull-based monitoring system that uses service discovery to scrape metrics from exporters, stores them as time-series data, evaluates alert rules, and integrates with Alertmanager and Grafana for alerting and visualization.
