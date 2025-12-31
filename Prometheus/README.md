# Prometheus – Complete Explanation 

## What is Prometheus?

Prometheus is an **open-source monitoring and alerting system** designed for **cloud-native and distributed systems**.

In simple words:

> **Prometheus collects metrics, stores them as time-series data, allows querying, and triggers alerts.**

Prometheus is widely used with:

* Kubernetes
* Microservices
* Cloud platforms

---

## What Problem Prometheus Solves

Modern systems:

* Are distributed
* Scale dynamically
* Have short-lived containers

Traditional monitoring tools struggle here.

Prometheus solves this by:

* Automatically discovering targets
* Pulling metrics dynamically
* Handling ephemeral workloads

---

## What Prometheus Monitors

Prometheus monitors **metrics**, not logs.

Examples:

* CPU usage
* Memory usage
* Request count
* Latency
* Error rate

Metrics are stored as **time-series data**.

---

## Core Components of Prometheus

### 1. Prometheus Server

* Scrapes metrics from targets
* Stores metrics in time-series database (TSDB)
* Executes PromQL queries

---

### 2. Targets

* Applications
* Nodes
* Pods
* Services

Targets expose metrics at:

```text
/metrics
```

---

### 3. Exporters

Exporters expose metrics in Prometheus format.

Common exporters:

* Node Exporter (node metrics)
* kube-state-metrics (Kubernetes object state)
* cAdvisor (container metrics)

Prometheus **never directly reads OS data**.

---

### 4. Alertmanager

* Receives alerts from Prometheus
* Groups and deduplicates alerts
* Routes alerts to Slack, Email, PagerDuty

---

### 5. Visualization Layer

Prometheus has a basic UI, but production uses:

* Grafana

---

## How Prometheus Works (Step-by-Step)

1. Application or system exposes metrics at `/metrics`
2. Prometheus discovers targets (static or dynamic)
3. Prometheus **pulls (scrapes)** metrics at intervals
4. Metrics stored as time-series data
5. PromQL queries analyze metrics
6. Alerts fire when conditions match
7. Dashboards visualize metrics

---

## Pull-Based Model (Very Important)

Prometheus uses a **pull-based architecture**.

Why pull-based?

* Easier debugging
* Better security
* Prometheus controls scrape frequency

Push is used only in rare cases (Pushgateway).

---

## PromQL (Prometheus Query Language)

PromQL is used to:

* Aggregate metrics
* Calculate rates
* Define alerts

Example:

```text
rate(http_requests_total[5m])
```

---

## Prometheus in Kubernetes (Production)

In Kubernetes, Prometheus uses:

* Service discovery
* Labels and selectors
* ServiceMonitor / PodMonitor

What it monitors:

* Nodes
* Pods
* Containers
* Control plane components

---

## Advantages of Prometheus

### 1. Cloud-Native Friendly

* Designed for dynamic environments
* Works perfectly with Kubernetes

---

### 2. Pull-Based Metrics Collection

* Simple architecture
* Easy to debug
* No agent dependency

---

### 3. Powerful Query Language (PromQL)

* Flexible
* Fast
* Time-series focused

---

### 4. Strong Kubernetes Integration

* Auto service discovery
* Label-based metrics
* Native ecosystem support

---

### 5. Reliable Alerting

* Alertmanager handles routing and deduplication
* Supports multiple alert channels

---

### 6. Open Source & CNCF Project

* Vendor-neutral
* Large community
* Production-proven

---

## Limitations of Prometheus

* Not ideal for long-term storage
* Not suitable for logs
* Single-node storage by default

These are solved using:

* Thanos
* Cortex
* Loki (for logs)

---

## Prometheus Use Cases in Production

* Infrastructure monitoring
* Application monitoring
* Kubernetes autoscaling (HPA)
* SLI/SLO monitoring
* Incident response

---
