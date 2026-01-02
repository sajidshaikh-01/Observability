# Loki in Production 

## What is Loki?

Loki is a **log aggregation system** designed by Grafana Labs, inspired by Prometheus.

**Key idea:**

* Prometheus indexes **metrics by labels**
* Loki indexes **logs by labels, not by log content**

This makes Loki:

* Cost‑efficient
* Highly scalable
* Perfectly integrated with Grafana and Kubernetes

---

## Why Loki?

Traditional log systems (like ELK) index full log content, which is expensive.

Loki:

* Stores logs as compressed chunks
* Indexes only labels (metadata)
* Uses object storage (S3, GCS, Azure Blob)

Result: **Cheaper + simpler + cloud‑native**

---

## Core Components (Architecture)

### 1. Promtail (or other agents)

* Runs on nodes
* Collects logs from:

  * Containers
  * Pods
  * System logs
* Adds labels (namespace, pod, app, node)
* Pushes logs to Loki

### 2. Loki

* Receives logs
* Stores logs in chunks
* Stores indexes separately
* Supports horizontal scaling

### 3. Object Storage

* S3 / GCS / Azure Blob
* Long‑term log storage

### 4. Grafana

* Query logs using **LogQL**
* Visualize logs + correlate with metrics

---

## Loki Architecture Modes

### 1. Single Binary (Dev / POC)

* All components in one process
* Not for production

### 2. Simple Scalable (Most Common)

* Read, Write, Backend separated
* Easy to operate
* Used by most production teams

### 3. Microservices Mode (Large Scale)

* Separate services:

  * Distributor
  * Ingester
  * Querier
  * Query Frontend
  * Compactor
* Used at very large scale

---

## How Loki Works (Flow)

1. Application writes logs to stdout
2. Container runtime stores logs on node
3. Promtail reads logs
4. Promtail adds labels
5. Logs pushed to Loki
6. Loki stores:

   * Index → boltdb-shipper
   * Chunks → Object Storage
7. Grafana queries logs using LogQL

---

## LogQL (Loki Query Language)

### Log Selection

```
{namespace="prod", app="api"}
```

### Filter Logs

```
{app="api"} |= "error"
```

### Regex Filter

```
{app="api"} |~ "5.."
```

### Metrics from Logs

```
count_over_time({app="api"} |= "error" [5m])
```

---

## How Loki is Used in Production (Kubernetes)

### Typical Setup

* Loki deployed via Helm
* Promtail as DaemonSet
* Grafana connected to Loki
* Storage: S3

### Labels Strategy (Very Important)

Good labels:

* namespace
* app
* pod
* cluster
* environment

Bad labels:

* request_id
* user_id
* dynamic values

---

## Production Best Practices

### 1. Label Carefully

* Low cardinality only
* Never label unique values

### 2. Use Object Storage

* S3 + lifecycle policies
* Cheap long‑term storage

### 3. Retention Policy

* Example:

  * 7–14 days hot logs
  * 30–90 days total retention

### 4. Multi‑Tenant Loki

* Separate logs per team
* Tenant ID based isolation

### 5. Secure Loki

* Authentication via:

  * Reverse proxy
  * OAuth / OIDC
* TLS everywhere

---

## Loki vs EFK (Quick Comparison)

| Feature    | Loki           | EFK              |
| ---------- | -------------- | ---------------- |
| Indexing   | Labels only    | Full log content |
| Cost       | Low            | High             |
| Query      | LogQL          | Lucene           |
| Storage    | Object storage | Local + object   |
| Kubernetes | Excellent      | Heavy            |

---

## When to Use Loki

Use Loki when:

* You already use Prometheus + Grafana
* You want low‑cost logging
* Kubernetes / Cloud‑native setup

Avoid Loki when:

* Heavy full‑text search required
* Legacy non‑container workloads

---

## Interview‑Ready One‑Liner

"Loki is a horizontally scalable, cost‑efficient log aggregation system that indexes logs using labels instead of content and integrates tightly with Prometheus and Grafana."

---

## Real Production Use Case

* Microservices on EKS
* Promtail collects container logs
* Loki stores logs in S3
* Grafana dashboards show:

  * Errors per service
  * Latency + logs correlation
  * Incident debugging

---
